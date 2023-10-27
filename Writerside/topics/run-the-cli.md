<title>Run the CLI</title>

The %product% cli is the part responsible for collecting statically defined log lines in your code base. It queries
the code base and sends only log lines to the api to be analysed.

The cli is available for `amd64 (64-bit x86)` and `Arm v8` architectures.

> During private beta all cli binaries will be provided by our team and are subject to change!

[comment]: <> (Download the cli for your platform at )

## Platform Compatibility {id="platform-compatibility"}

<table id="platform-compatibility-table" style="header-row">
<tr>
    <td>OS</td>
    <td>Architecture</td>
</tr>
<tr>
    <td>Windows</td>
    <td>64-bit x86 and Arm64</td>
</tr>
<tr>
    <td>Linux</td>
    <td>64-bit x86 / MUSL 64-bit x86 and Arm64 / Bionic Arm64</td>
</tr>
</table>

## Cli Commands {id=cli-commands}

<table id="cli-commands-table" style="header-row">
<tr>
    <td>Command</td>
    <td>Description</td>
</tr>
<tr>
    <td><code>lookup</code></td>
    <td>Executes log line source code lookup</td>
</tr>
</table>

## Cli Arguments {id=cli-arguments}

<table id="cli-arguments-table" style="header-row">
<tr>
    <td>Argument</td>
    <td>Description</td>
</tr>
<tr>
    <td><code>--source</code></td>
    <td>Path where source code to  be analyzed can be found</td>
</tr>
<tr>
    <td><code>--api-key</code></td>
    <td>The organization api-key</td>
</tr>
<tr>
    <td><code>--api-url</code></td>
    <td>Your host address where the api is running</td>
</tr>
<tr>
    <td><code>--timeout</code></td>
    <td>The timeout of the cli http request to the api</td>
</tr>
<tr>
    <td><code>--debug</code></td>
    <td>Enables debug logging</td>
</tr>
<tr>
    <td><code>--quiet</code></td>
    <td>Enables quite mode</td>
</tr>
</table>

## How to use it?

You can hook the cli into one of your CI/CD pipeline stage or run it locally on your machine.

`ubat lookup --source <your-repo-path> --api-url https://<your-host-address> --api-key <your-api-key>`  