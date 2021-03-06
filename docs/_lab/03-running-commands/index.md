---
title: Running Commands
difficulty: Basic
time: Approximately 5 minutes
---


>> You can use Bolt to run arbitrary commands on a set of remote hosts. Let's see that in practice before we move on to more advanced features. Choose the exercise based on the operating system of your test targets.

Boltを使ってリモートホスト上でコマンドを実行することができます。より高度な機能に移る前に、実践してみてみましょう。テスト対象のOSに基づいた演習を選択してください。

- [Running Shell Commands on Linux Targets](#running-shell-commands-on-linux-targets)
- [Running PowerShell Commands on Windows Targets](#running-powershell-commands-on-windows-targets)

## Prerequisites（前提条件）

>> Complete the following before you start this lesson:

レッスンを始める前に、以下を完了させてください。：

- [Installing Bolt](../01-installing-bolt)
- [Setting Up Test Targets](../02-acquiring-targets)

## Running Shell Commands on Linux Targets

>> Bolt by default uses SSH for transport. If you can connect to systems remotely, you can use Bolt to run shell commands. It reuses your existing SSH configuration for authentication, which is typically provided in `~/.ssh/config`.

BoltはデフォルトでSSH転送を使用します。もしシステムにリモート接続できれば、Boltでシェルコマンドを実行できます。通常は`~/.ssh/config`に設定されている、既存のSSH認証設定を使用します。

>> To run a command against a remote Linux target, use the following command syntax:

リモートのLinuxでコマンドを実行するには、次のコマンドを使用します。:
```shell
bolt command run <command> --targets <targets>
```


>> To run a command against a remote target using a username and password rather than keys use the following syntax:

リモートのターゲットに対して、Keyではなくユーザー名とパスワードでコマンドを実行するには、次のコマンドを使用します。:
```shell
bolt command run <command> --targets <targets> --user <user> --password <password>
```
>> Run the `uptime` command to view how long the system has been running. If you are using existing targets on your system, replace `target1` with the address for your target.

どれだけの期間システムが動いているか表する`uptime`コマンドを実行します。既存のターゲットが存在する場合は、`target1`を実際のターゲットに置き換えてください。

```shell
bolt command run uptime --targets target1
```

The result:
```
Started on target1...
Finished on target1:
  STDOUT:
     22:42:18 up 16 min,  0 users,  load average: 0.00, 0.01, 0.03
Successful on 1 target: target1
Ran on 1 target in 0.42 seconds

```

>> > **Tip:** If you receive the error `Host key verification failed` make sure the correct host keys are in your `known_hosts` file, set `StrictHostKeyChecking=no` in your SSH config, or pass `--no-host-key-check` to future Bolt commands.

**ヒント:** もし`Host key verification failed`エラーが返った場合は、`known_hosts` ファイルに正しいホスト鍵があることを確認するか、SSH 設定で `StrictHostKeyChecking=no` を設定するか、将来の Bolt コマンドに `--no-host-key-check` を渡すようにしてください。（※訳注：ここはよくわからない…`--no-host-key-check`で動く様になったのでこのオプションをつけろという意味なのだろう…）

>> Run the 'uptime' command on multiple targets by passing a comma-separated list. If you are using existing targets on your system, replace `target1,target2,target3` with addresses for your targets.

'uptime'コマンドを複数のターゲットで連続実行するには、カンマ区切りのリストにして渡します。既にターゲットになるシステムがある場合は、`target1,target2,target3`を自分のターゲットのアドレスに置き換えて下さい。

```shell
bolt command run uptime --targets target1,target2,target3
```

The result:
```
Started on target1...
Started on target3...
Started on target2...
Finished on target2:
  STDOUT:
     21:03:37 up  2:06,  0 users,  load average: 0.00, 0.01, 0.03
Finished on target3:
  STDOUT:
     21:03:37 up  2:05,  0 users,  load average: 0.08, 0.03, 0.05
Finished on target1:
  STDOUT:
     21:03:37 up  2:07,  0 users,  load average: 0.00, 0.01, 0.05
Successful on 3 targets: target1,target2,target3
Ran on 3 targets in 0.52 seconds
```

## Running PowerShell Commands on Windows Targets

>> Bolt can communicate over WinRM and execute PowerShell commands when running Windows targets. To run a command against a remote Windows target, use the following command syntax:

Boltは、Windowsをターゲットにする場合、WinRMで通信しPowerShellコマンドを実行します。リモートのWindowsをターゲットにコマンドを実行するには、次の構文を使用します。:

```shell
bolt command run <command> --targets winrm://<target> --user <user> --password <password>
```

>> Note the `winrm://` prefix for the target address. Also note the `--username` and `--password` flags for passing authentication information. In addition, unless you have set up SSL for WinRM communication, you must supply the `--no-ssl` flag. Otherwise running a Bolt command will result in an `unknown protocol` error.

targetのアドレスにあるプレフィクス`winrm://`に注意してください。また、認証情報を渡すための `--username` と `--password` フラグにも注意してください。更に、WinRMの通信にSSLを設定していない場合は、`--no-ssl`フラグを指定する必要があります。それをせずにBoltコマンドを実行すると `unknown protocol` エラーが発生します。

```shell
bolt command run <command> --no-ssl --targets winrm://<target>,winrm://<target> --user <user> --password <password>
```

>> Run the following command to list all of the processes running on a remote machine. Note that this command uses the `windows` group defined in the `inventory.yaml` file. Since the inventory file is configured to not use SSL, the `--no-ssl` flag is not needed.

リモートマシン実行してリモートマシンで実行されているすべてのプロセスをリストアップします。このコマンドは `inventory.yaml` ファイルで定義された `windows` グループを使うことに注意する必要があります。インベントリファイルはSSLを使わないように設定されているので、`--no-ssl`フラグは不要となります。

```shell
bolt command run "gps | select ProcessName" --targets windows
```

>> Use the following syntax to list all of the processes running on multiple remote machines.

次の構文を使用して、複数のリモートマシンで実行されているプロセスを一覧します。

```shell
bolt command run <command> --targets winrm://<target>,winrm://<target> --user <user> --password <password>
```

## Next Steps

Now that you know how to use Bolt to run adhoc commands you can move on to:

[Running Scripts](../04-running-scripts)
