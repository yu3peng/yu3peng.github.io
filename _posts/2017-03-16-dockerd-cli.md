---
layout: post
title: DOCKERD CLI 代码解析
categories: Docker
description:
keywords:
---

DOCKERD CLI 代码解析

## cobra示例

docker将客户端程序和服务器程序分为docker和dockerd，两者的命令行解析都采用第三方库：[cobra](https://github.com/spf13/cobra)

首先用个[小程序](../../../../2017/02/17/cobra/#little-example)介绍一下cobra的简单用法：

## 基本用法是这四步：
1. 主命令
2. 子命令
3. 添加选项
4. 执行命令

## dockerd职责
docker接受并解析客户端的操作指令，然后访问dockerd相应的url，让dockerd做实际的处理。
dockerd实际上可简化为一个web后台，提供各种web接口API让docker访问。

## dockerd cli 代码解析
dcokerd的入口函数在文件docker/cmd/dockerd/docker.go中
### 从main函数开始
```go
func main() {
    if reexec.Init() {
        return
    }

    // Set terminal emulation based on platform as required.
    _, stdout, stderr := term.StdStreams()
    logrus.SetOutput(stderr)

    cmd := newDaemonCommand()
    cmd.SetOutput(stdout)
    if err := cmd.Execute(); err != nil {
        fmt.Fprintf(stderr, "%s\n", err)
        os.Exit(1)
    }
}
```

### newDaemonCommand继续解析
```go
func newDaemonCommand() *cobra.Command {
    opts := daemonOptions{
        daemonConfig: config.New(),
        common:       cliflags.NewCommonOptions(),
    }

    // 主命令
    cmd := &cobra.Command{
        Use:           "dockerd [OPTIONS]",
        Short:         "A self-sufficient runtime for containers.",
        SilenceUsage:  true,
        SilenceErrors: true,
        Args:          cli.NoArgs,
        RunE: func(cmd *cobra.Command, args []string) error {
            opts.flags = cmd.Flags()

            // 主命令回调函数
            return runDaemon(opts)
        },
    }
    cli.SetupRootCommand(cmd)

    flags := cmd.Flags()
    flags.BoolVarP(&opts.version, "version", "v", false, "Print version information and quit")

    // 默认配置文件，unix、linux为"/etc/docker/daemon.json"，windows和Solaris为""
    flags.StringVar(&opts.configFile, flagDaemonConfigFile, defaultDaemonConfigFile, "Daemon configuration file")

    // 通过install，从flag获取选项值，并将相应值传到各个模块
    // cli界面上的呈现的命令都能在以下函数中找到
    opts.common.InstallFlags(flags)
    installConfigFlags(opts.daemonConfig, flags)

    // 试验用
    installServiceFlags(flags)

    return cmd
}
```

### 命令解析完就会执行函数runDaemon
```go
func runDaemon(opts daemonOptions) error {
    if opts.version {
        showVersion()
        return nil
    }

    daemonCli := NewDaemonCli()

    // Windows specific settings as these are not defaulted.
    if runtime.GOOS == "windows" {
        if opts.daemonConfig.Pidfile == "" {
            opts.daemonConfig.Pidfile = filepath.Join(opts.daemonConfig.Root, "docker.pid")
        }
        if opts.configFile == "" {
            opts.configFile = filepath.Join(opts.daemonConfig.Root, `config\daemon.json`)
        }
    }

    // On Windows, this may be launching as a service or with an option to
    // register the service.
    // unix、linux下为空函数
    stop, err := initService(daemonCli)
    if err != nil {
        logrus.Fatal(err)
    }

    if stop {
        return nil
    }

    // 启动一个server
    err = daemonCli.start(opts)
    notifyShutdown(err)
    return err
}
```

### 启动一个server
```go
func (cli *DaemonCli) start(opts daemonOptions) (err error) {
    stopc := make(chan bool)
    defer close(stopc)

    // warn from uuid package when running the daemon
    uuid.Loggerf = logrus.Warnf

    opts.common.SetDefaultOptions(opts.flags)

    if cli.Config, err = loadDaemonCliConfig(opts); err != nil {
        return err
    }
    cli.configFile = &opts.configFile
    cli.flags = opts.flags

    if opts.common.TrustKey == "" {
        opts.common.TrustKey = filepath.Join(
            getDaemonConfDir(cli.Config.Root),
            cliflags.DefaultTrustKeyFile)
    }

    if cli.Config.Debug {
        debug.Enable()
    }

    if cli.Config.Experimental {
        logrus.Warn("Running experimental build")
    }

    logrus.SetFormatter(&logrus.TextFormatter{
        TimestampFormat: jsonlog.RFC3339NanoFixed,
        DisableColors:   cli.Config.RawLogs,
    })

    if err := setDefaultUmask(); err != nil {
        return fmt.Errorf("Failed to set umask: %v", err)
    }

    if len(cli.LogConfig.Config) > 0 {
        if err := logger.ValidateLogOpts(cli.LogConfig.Type, cli.LogConfig.Config); err != nil {
            return fmt.Errorf("Failed to set log opts: %v", err)
        }
    }

    // Create the daemon root before we create ANY other files (PID, or migrate keys)
    // to ensure the appropriate ACL is set (particularly relevant on Windows)
    if err := daemon.CreateDaemonRoot(cli.Config); err != nil {
        return err
    }

    if cli.Pidfile != "" {
        pf, err := pidfile.New(cli.Pidfile)
        if err != nil {
            return fmt.Errorf("Error starting daemon: %v", err)
        }
        defer func() {
            if err := pf.Remove(); err != nil {
                logrus.Error(err)
            }
        }()
    }

    serverConfig := &apiserver.Config{
        Logging:     true,
        SocketGroup: cli.Config.SocketGroup,
        Version:     dockerversion.Version,
        EnableCors:  cli.Config.EnableCors,
        CorsHeaders: cli.Config.CorsHeaders,
    }

    if cli.Config.TLS {
        tlsOptions := tlsconfig.Options{
            CAFile:   cli.Config.CommonTLSOptions.CAFile,
            CertFile: cli.Config.CommonTLSOptions.CertFile,
            KeyFile:  cli.Config.CommonTLSOptions.KeyFile,
        }

        if cli.Config.TLSVerify {
            // server requires and verifies client's certificate
            tlsOptions.ClientAuth = tls.RequireAndVerifyClientCert
        }
        tlsConfig, err := tlsconfig.Server(tlsOptions)
        if err != nil {
            return err
        }
        serverConfig.TLSConfig = tlsConfig
    }

    if len(cli.Config.Hosts) == 0 {
        cli.Config.Hosts = make([]string, 1)
    }

    api := apiserver.New(serverConfig)
    cli.api = api

    for i := 0; i < len(cli.Config.Hosts); i++ {
        var err error
        if cli.Config.Hosts[i], err = dopts.ParseHost(cli.Config.TLS, cli.Config.Hosts[i]); err != nil {
            return fmt.Errorf("error parsing -H %s : %v", cli.Config.Hosts[i], err)
        }

        protoAddr := cli.Config.Hosts[i]
        protoAddrParts := strings.SplitN(protoAddr, "://", 2)
        if len(protoAddrParts) != 2 {
            return fmt.Errorf("bad format %s, expected PROTO://ADDR", protoAddr)
        }

        proto := protoAddrParts[0]
        addr := protoAddrParts[1]

        // It's a bad idea to bind to TCP without tlsverify.
        if proto == "tcp" && (serverConfig.TLSConfig == nil || serverConfig.TLSConfig.ClientAuth != tls.RequireAndVerifyClientCert) {
            logrus.Warn("[!] DON'T BIND ON ANY IP ADDRESS WITHOUT setting --tlsverify IF YOU DON'T KNOW WHAT YOU'RE DOING [!]")
        }
        ls, err := listeners.Init(proto, addr, serverConfig.SocketGroup, serverConfig.TLSConfig)
        if err != nil {
            return err
        }
        ls = wrapListeners(proto, ls)
        // If we're binding to a TCP port, make sure that a container doesn't try to use it.
        if proto == "tcp" {
            if err := allocateDaemonPort(addr); err != nil {
                return err
            }
        }
        logrus.Debugf("Listener created for HTTP on %s (%s)", proto, addr)
        api.Accept(addr, ls...)
    }

    if err := migrateKey(cli.Config); err != nil {
        return err
    }

    // FIXME: why is this down here instead of with the other TrustKey logic above?
    cli.TrustKeyPath = opts.common.TrustKey

    registryService := registry.NewService(cli.Config.ServiceOptions)
    containerdRemote, err := libcontainerd.New(cli.getLibcontainerdRoot(), cli.getPlatformRemoteOptions()...)
    if err != nil {
        return err
    }
    signal.Trap(func() {
        cli.stop()
        <-stopc // wait for daemonCli.start() to return
    })

    // Notify that the API is active, but before daemon is set up.
    preNotifySystem()

    d, err := daemon.NewDaemon(cli.Config, registryService, containerdRemote)
    if err != nil {
        return fmt.Errorf("Error starting daemon: %v", err)
    }

    if cli.Config.MetricsAddress != "" {
        if !d.HasExperimental() {
            return fmt.Errorf("metrics-addr is only supported when experimental is enabled")
        }
        if err := startMetricsServer(cli.Config.MetricsAddress); err != nil {
            return err
        }
    }

    name, _ := os.Hostname()

    c, err := cluster.New(cluster.Config{
        Root:                   cli.Config.Root,
        Name:                   name,
        Backend:                d,
        NetworkSubnetsProvider: d,
        DefaultAdvertiseAddr:   cli.Config.SwarmDefaultAdvertiseAddr,
        RuntimeRoot:            cli.getSwarmRunRoot(),
    })
    if err != nil {
        logrus.Fatalf("Error creating cluster component: %v", err)
    }

    // Restart all autostart containers which has a swarm endpoint
    // and is not yet running now that we have successfully
    // initialized the cluster.
    d.RestartSwarmContainers()

    logrus.Info("Daemon has completed initialization")

    logrus.WithFields(logrus.Fields{
        "version":     dockerversion.Version,
        "commit":      dockerversion.GitCommit,
        "graphdriver": d.GraphDriverName(),
    }).Info("Docker daemon")

    cli.d = d

    // initMiddlewares needs cli.d to be populated. Dont change this init order.
    if err := cli.initMiddlewares(api, serverConfig); err != nil {
        logrus.Fatalf("Error creating middlewares: %v", err)
    }
    d.SetCluster(c)
    initRouter(api, d, c)

    cli.setupConfigReloadTrap()

    // The serve API routine never exits unless an error occurs
    // We need to start it as a goroutine and wait on it so
    // daemon doesn't exit
    serveAPIWait := make(chan error)
    go api.Wait(serveAPIWait)

    // after the daemon is done setting up we can notify systemd api
    notifySystem()

    // Daemon is fully initialized and handling API traffic
    // Wait for serve API to complete
    errAPI := <-serveAPIWait
    c.Cleanup()
    shutdownDaemon(d)
    containerdRemote.Cleanup()
    if errAPI != nil {
        return fmt.Errorf("Shutting down due to ServeAPI error: %v", errAPI)
    }

    return nil
}
```

