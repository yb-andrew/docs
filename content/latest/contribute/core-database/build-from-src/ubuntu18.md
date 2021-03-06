
## Installing necessary packages

Update packages on your system, install development tools and additional packages:

```
sudo apt-get update
sudo apt-get install uuid-dev libbz2-dev libreadline-dev maven ninja-build
```

## Getting build tools ready

Make sure `cmake` / `ctest` binaries are at least version 3. On CentOS one way to achive this is to symlink them into `/usr/local/bin`.

```
sudo ln -s /usr/bin/cmake3 /usr/local/bin/cmake
sudo ln -s /usr/bin/ctest3 /usr/local/bin/ctest
```

You could also symlink them into another directory that is on your PATH.

{{< note title="Note on Linuxbrew" >}}
We also use [Linuxbrew](https://github.com/linuxbrew/brew) to provide some of the third-party dependencies on CentOS.

During the build we install Linuxbrew in a separate directory, `~/.linuxbrew-yb-build/linuxbrew-<version>`, so that it does not conflict with any other Linuxbrew installation on your workstation, and does not contain any unnecessary packages that would interfere with the build.

We don't need to add `~/.linuxbrew-yb-build/linuxbrew-<version>/bin` to PATH. The build scripts will automatically discover this Linuxbrew installation.
{{< /note >}}


