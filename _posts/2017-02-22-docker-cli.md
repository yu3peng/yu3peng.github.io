---
layout: post
title: DOCKER CLI 代码解析
categories: Docker
description:
keywords:
---

DOCKER CLI 代码解析

## cobra示例

docker将客户端程序和服务器程序分为docker和dockerd，两者的命令行解析都采用第三方库：[cobra](https://github.com/spf13/cobra)

首先用个[小程序](../../../../2017/02/17/cobra/#little-example)介绍一下cobra的简单用法：

## 基本用法是这四步：
1. 主命令
2. 子命令
3. 添加选项
4. 执行命令

##  docker职责
接受并解析客户端的操作指令。

## docker cli 代码解析
### 从main函数开始
dcokerd的入口函数在文件docker/cmd/docker/docker.go中
```go
func main() {
    // 日志采用第三方库logrus
    stdin, stdout, stderr := term.StdStreams()
    logrus.SetOutput(stderr)

    dockerCli := command.NewDockerCli(stdin, stdout, stderr)

    // 主命令、子命令、添加选项都在此函数中
    cmd := newDockerCommand(dockerCli)

    // 执行命令
    if err := cmd.Execute(); err != nil {
        if sterr, ok := err.(cli.StatusError); ok {
            if sterr.Status != "" {
                fmt.Fprintln(stderr, sterr.Status)
            }
            // StatusError should only be used for errors, and all errors should
            // have a non-zero exit status, so never exit with 0
            if sterr.StatusCode == 0 {
                os.Exit(1)
            }
            os.Exit(sterr.StatusCode)
        }
        fmt.Fprintln(stderr, err)
        os.Exit(1)
    }
}
```

### newDockerCommand继续解析
```go
func newDockerCommand(dockerCli *command.DockerCli) *cobra.Command {
    // 选项结构对象，通过传入的选项选项参数进行填充
    opts := cliflags.NewClientOptions()

    // 选项集合
    var flags *pflag.FlagSet

    // 主命令
    cmd := &cobra.Command{
        Use:              "docker [OPTIONS] COMMAND [ARG...]",
        Short:            "A self-sufficient runtime for containers",
        SilenceUsage:     true,
        SilenceErrors:    true,
        TraverseChildren: true,
        Args:             noArgs,
        RunE: func(cmd *cobra.Command, args []string) error {
            if opts.Version {
                showVersion()
                return nil
            }
            return dockerCli.ShowHelp(cmd, args)
        },
        PersistentPreRunE: func(cmd *cobra.Command, args []string) error {
            // daemon command is special, we redirect directly to another binary
            if cmd.Name() == "daemon" {
                return nil
            }
            // flags must be the top-level command flags, not cmd.Flags()
            opts.Common.SetDefaultOptions(flags)
            dockerPreRun(opts)
            if err := dockerCli.Initialize(opts); err != nil {
                return err
            }
            return isSupported(cmd, dockerCli.Client().ClientVersion(), dockerCli.HasExperimental())
        },
    }

    // 设置默认的处理方式
    cli.SetupRootCommand(cmd)

    // 主命令添加选项
    flags = cmd.Flags()
    flags.BoolVarP(&opts.Version, "version", "v", false, "Print version information and quit")
    flags.StringVar(&opts.ConfigDir, "config", cliconfig.Dir(), "Location of client config files")

    // 添加公共选项
    opts.Common.InstallFlags(flags)

    setFlagErrorFunc(dockerCli, cmd, flags, opts)

    setHelpFunc(dockerCli, cmd, flags, opts)

    // 设置命令的输出
    cmd.SetOutput(dockerCli.Out())

    // 添加daemon选项，服务器功能在docker命令中已经作废，这里只是提示“请使用dockerd”
    cmd.AddCommand(newDaemonCommand())

    // 子命令
    commands.AddCommands(cmd, dockerCli)

    // 参数合法性验证
    setValidateArgs(dockerCli, cmd, flags, opts)

    return cmd
}
```

这里主要是主命令的解析，子命令还需要继续看这个函数
```go
    // 子命令
    commands.AddCommands(cmd, dockerCli)
```

### AddCommands添加子命令
```go
// AddCommands adds all the commands from cli/command to the root command
func AddCommands(cmd *cobra.Command, dockerCli *command.DockerCli) {
    cmd.AddCommand(
        // checkpoint
        checkpoint.NewCheckpointCommand(dockerCli),

        // container
        container.NewContainerCommand(dockerCli),
        container.NewRunCommand(dockerCli),

        // image
        image.NewImageCommand(dockerCli),
        image.NewBuildCommand(dockerCli),

        // node
        node.NewNodeCommand(dockerCli),

        // network
        network.NewNetworkCommand(dockerCli),

        // plugin
        plugin.NewPluginCommand(dockerCli),

        // registry
        registry.NewLoginCommand(dockerCli),
        registry.NewLogoutCommand(dockerCli),
        registry.NewSearchCommand(dockerCli),

        // secret
        secret.NewSecretCommand(dockerCli),

        // service
        service.NewServiceCommand(dockerCli),

        // system
        system.NewSystemCommand(dockerCli),
        system.NewVersionCommand(dockerCli),

        // stack
        stack.NewStackCommand(dockerCli),
        stack.NewTopLevelDeployCommand(dockerCli),

        // swarm
        swarm.NewSwarmCommand(dockerCli),

        // volume
        volume.NewVolumeCommand(dockerCli),

        // legacy commands may be hidden
        hide(system.NewEventsCommand(dockerCli)),
        hide(system.NewInfoCommand(dockerCli)),
        hide(system.NewInspectCommand(dockerCli)),
        hide(container.NewAttachCommand(dockerCli)),
        hide(container.NewCommitCommand(dockerCli)),
        hide(container.NewCopyCommand(dockerCli)),
        hide(container.NewCreateCommand(dockerCli)),
        hide(container.NewDiffCommand(dockerCli)),
        hide(container.NewExecCommand(dockerCli)),
        hide(container.NewExportCommand(dockerCli)),
        hide(container.NewKillCommand(dockerCli)),
        hide(container.NewLogsCommand(dockerCli)),
        hide(container.NewPauseCommand(dockerCli)),
        hide(container.NewPortCommand(dockerCli)),
        hide(container.NewPsCommand(dockerCli)),
        hide(container.NewRenameCommand(dockerCli)),
        hide(container.NewRestartCommand(dockerCli)),
        hide(container.NewRmCommand(dockerCli)),
        hide(container.NewStartCommand(dockerCli)),
        hide(container.NewStatsCommand(dockerCli)),
        hide(container.NewStopCommand(dockerCli)),
        hide(container.NewTopCommand(dockerCli)),
        hide(container.NewUnpauseCommand(dockerCli)),
        hide(container.NewUpdateCommand(dockerCli)),
        hide(container.NewWaitCommand(dockerCli)),
        hide(image.NewHistoryCommand(dockerCli)),
        hide(image.NewImagesCommand(dockerCli)),
        hide(image.NewImportCommand(dockerCli)),
        hide(image.NewLoadCommand(dockerCli)),
        hide(image.NewPullCommand(dockerCli)),
        hide(image.NewPushCommand(dockerCli)),
        hide(image.NewRemoveCommand(dockerCli)),
        hide(image.NewSaveCommand(dockerCli)),
        hide(image.NewTagCommand(dockerCli)),
    )

}
```

这里添加了很多子命令，这些命令都是一级子命令，后面还可跟二级子命令。
有些是被hide函数包裹的，表示这些函数是之前的版本使用的，在新的版本中不推荐使用，
在实际环境中可以通过设置DOCKER_HIDE_LEGACY_COMMANDS环境变量来实现，具体见[HIDE LEGACY COMMANDS](../../../../2017/02/24/docker-env/#docker_hide_legacy_commands)。

我们拿一条命令来继续解析
```go
hide(image.NewHistoryCommand(dockerCli)),
```
### NewHistoryCommand函数
```go
func NewHistoryCommand(dockerCli *command.DockerCli) *cobra.Command {
    var opts historyOptions

    cmd := &cobra.Command{
        Use:   "history [OPTIONS] IMAGE",
        Short: "Show the history of an image",
        Args:  cli.ExactArgs(1),

        // 命令执行函数，错误类型的变量作为返回值
        RunE: func(cmd *cobra.Command, args []string) error {
            opts.image = args[0]
            return runHistory(dockerCli, opts)
        },
    }

    // 添加选项
    flags := cmd.Flags()

    flags.BoolVarP(&opts.human, "human", "H", true, "Print sizes and dates in human readable format")
    flags.BoolVarP(&opts.quiet, "quiet", "q", false, "Only show numeric IDs")
    flags.BoolVar(&opts.noTrunc, "no-trunc", false, "Don't truncate output")

    return cmd
}
```
当我们执行docker history时，就会执行命令中定义的函数，进而执行runHistory(),该函数根据选项执行不同的操作。

### runHistory函数
```go
func runHistory(dockerCli *command.DockerCli, opts historyOptions) error {
    ctx := context.Background()

    history, err := dockerCli.Client().ImageHistory(ctx, opts.image)
    if err != nil {
        return err
    }

    w := tabwriter.NewWriter(dockerCli.Out(), 20, 1, 3, ' ', 0)

    if opts.quiet {
        for _, entry := range history {
            if opts.noTrunc {
                fmt.Fprintf(w, "%s\n", entry.ID)
            } else {
                fmt.Fprintf(w, "%s\n", stringid.TruncateID(entry.ID))
            }
        }
        w.Flush()
        return nil
    }

    var imageID string
    var createdBy string
    var created string
    var size string

    fmt.Fprintln(w, "IMAGE\tCREATED\tCREATED BY\tSIZE\tCOMMENT")
    for _, entry := range history {
        imageID = entry.ID
        createdBy = strings.Replace(entry.CreatedBy, "\t", " ", -1)
        if !opts.noTrunc {
            createdBy = stringutils.Ellipsis(createdBy, 45)
            imageID = stringid.TruncateID(entry.ID)
        }

        if opts.human {
            created = units.HumanDuration(time.Now().UTC().Sub(time.Unix(entry.Created, 0))) + " ago"
            size = units.HumanSizeWithPrecision(float64(entry.Size), 3)
        } else {
            created = time.Unix(entry.Created, 0).Format(time.RFC3339)
            size = strconv.FormatInt(entry.Size, 10)
        }

        fmt.Fprintf(w, "%s\t%s\t%s\t%s\t%s\n", imageID, created, createdBy, size, entry.Comment)
    }
    w.Flush()
    return nil
}
```
