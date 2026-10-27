# Install Percona Server for MySQL {{vers}} from a binary tarball

A binary tarball is one file that contains the server binaries and support files. The `tar` command bundles the files, and `gzip` compresses them.

Use a binary tarball in the following cases:

- You need to run more than one version of Percona Server for MySQL on the same host

- Percona does not provide a package for your Linux distribution

For all other cases, use [APT]([apt-repo.md](http://apt-repo.md)) or [DNF]([yum-repo.md](http://yum-repo.md)) packages.

See the list of [binary tarballs available]([binary-tarball-names.md](http://binary-tarball-names.md)) for each Percona Server for MySQL version. Select the tarball that matches your environment. Download the binary tarballs from the `Linux - Generic` [section :octicons-link-external-16:](https://www.percona.com/downloads/Percona-Server-{{vers}}/LATEST/binary/tarball/) on the download page.  
  
## Check your system

Run the following command to find the glibc version of your system:

```
ldd --version
```

The first line of the output ends with the glibc version. Select the tarball with that glibc version. If Percona does not publish that version, select the newest published glibc version that is older than yours. A tarball built for an older glibc runs on a newer glibc. If every published tarball is newer than your glibc, Percona Server for MySQL does not support the operating system.

Run the following command to find the CPU architecture:

```
uname -m
```

Select a tarball that matches the architecture, for example `x86_64`.

## Install operating system libraries

The tarball bundles many libraries, including `libaio` and `libnuma`. `bin/mysqld` still needs OpenSSL from the operating system, and some features need other libraries. The package names are on the [Operating system libraries](os-libraries.md) page.

On Ubuntu 26.04, install the OpenSSL package the glibc 2.35 tarball needs before you start the server:

```
sudo apt install libssl3t64
```

## Create the `mysql` user

Create the `mysql` group and user. The server runs as the `mysql` user:

```
sudo groupadd mysql
sudo useradd -r -g mysql -s /bin/false mysql
```

Fetch and extract the correct binary tarball. The following steps use Ubuntu 26.04 as an example. Ubuntu 26.04 has glibc 2.43. These steps download the glibc 2.35 tarball, the newest published build that runs on that system.

1. Download the tarball:

    ```
    wget https://downloads.percona.com/downloads/Percona-Server-8.4/Percona-Server-8.4.11-11/binary/tarball/Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz
    ```

    ??? example "Expected output"
        ```{.text .no-copy}
        --YYYY-MM-DD hh:mm:ss--  https://downloads.percona.com/...
        Saving to: 'Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz'
        Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz   100%[=================>]  xxx MB  xx.x MB/s    in xx s
        YYYY-MM-DD hh:mm:ss (xx.x MB/s) - 'Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz' saved [xxxxxx/xxxxxx]
        ```

2. Download the checksum file:

    ```
    wget https://downloads.percona.com/downloads/Percona-Server-8.4/Percona-Server-8.4.11-11/binary/tarball/Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz.sha256sum
    ```

    ??? example "Expected output"
        ```{.text .no-copy}
        --YYYY-MM-DD hh:mm:ss--  https://downloads.percona.com/...
        Saving to: 'Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz.sha256sum'
        Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz.sha256sum   100%[=================>]  xxx B  xx.x KB/s    in xx s
        YYYY-MM-DD hh:mm:ss (xx.x KB/s) - 'Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz.sha256sum' saved [xxxxxx/xxxxxx]
        ```

3. Verify the tarball:

    ```
    sha256sum -c Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz.sha256sum
    ```

    ??? example "Expected output"
        ```{.text .no-copy}
        Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz: OK
        ```

4. Extract the tarball to `/usr/local`:

    ```
    sudo tar -xzf Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35.tar.gz -C /usr/local
    ```

    ??? example "Expected output"
        ```{.text .no-copy}
        (No output on success.)
        ```

5. Create the `/usr/local/mysql` symbolic link. The [Post-installation](post-installation.md) guide uses this path:

    ```
    sudo ln -s /usr/local/Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35 /usr/local/mysql
    ```

6. Assign ownership of the extracted directory to the `mysql` user:

    ```
    sudo chown -R mysql:mysql /usr/local/Percona-Server-8.4.11-11-Linux.x86_64.glibc2.35
    ```

7. Change to the `/usr/local/mysql` directory:

    ```
    cd /usr/local/mysql
    ```

## After extraction

The tarball does not start the server. Complete the following tasks:

1. Initialize the data directory with `bin/mysqld --initialize` or `bin/mysqld --initialize-insecure`.

2. Start the server with `bin/mysqld_safe` or a systemd service.

The `--initialize` option prints a temporary password for the `root` user. Copy the password.

Do not start `bin/mysqld` for normal operation before you initialize the data directory.

For step-by-step instructions, see [Post-installation](post-installation.md). For the next tasks, see [Quickstart - First five minutes (security and stability)](first-five-minutes.md).
