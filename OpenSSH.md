# OpenSSH: Cross OS Cheatsheet
- [OpenSSH Manual](https://www.openssh.com)
- How to:: #ssh #howtocomputer #howto #linux #macos #shell #bash #SSH howtossh how to ssh
> **Note:** This file was imported from Roam Research and edited with One Markdown. Not all MD editors are the same, and some include other features. Certain content may not appear correctly in this document, depending on your MD viewer.

> **Note:** This document is not complete—a work in progress, especially the Windows section. Any input or suggestions you'd like to contribute are more than welcome.!

# Basic SSH Commands (OS Agnostic)
- **Login** to a device: `ssh user@hostname -p 22`
- **End** a connection: `logout`

---

# MacOS

## Enable/Disable SSH
> Remote login via ssh is included as a system feature, and must be enabled through System Settings.

- **Using MacOS GUI:**
    - Navigate to `System Settings > General > Sharing > Advanced > Remote Login` and enable Remote Login

- **Using CLI:**
    - Toggle the SSH daemon: `sudo systemsetup -setremotelogin <on|off>`
    - Check the daemon status: `sudo systemsetup -getremotelogin`

## Configuration

### General Info
- **Keys** are stored in: `~/.ssh/`
- **Client Config:** `~/.ssh/config`


### How to: Configure Named Hosts (Nicknames)
1. Open Client config: `sudo nano /etc/ssh/ssh_config`
2. Create a new host above `Host *`; Settings are only changed once per read, subsequent changes of a variable are ignored.
- *Add a known host:*
  ```shell
  Host <arbitrary name>
    Hostname <remote host hostname/IP address>
    User <remote host username>
    Port <remote host listener port; optional>
    PubKeyAuthentication yes|no
    IdentityFile <public key filepath>
  ```
    - This allows you to login with a nickname `ssh <arbitrary name>`
    - Specifying an identity file ensures a particular public key is used & allows password-less authentication. See Configure Key Pairs (from client) for more info.
    - *Input is case sensitive
- > If there is a permissions issue, try: `chmod 600 <path to config file>`


### How to: Generate Key Pairs (from client)
- **[REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) Guided:**
    - Type `ssh-keygen` Follow the prompts to setup a key pair.
        - Default keys should be automatically saved to:
            - Public: `~/.ssh/id_rsa.pub`
            - Private: `~/.ssh/id_rsa`
- **Specific:**
    - Type: 
    ```shell
    ssh-keygen -t <rsa|ed25519|other> -b <bits> -N <passphrase|""> -f ~/.ssh/<keyPair name>
    ```
    - `-t`: Type
    - `-b`: Encryption bits. 2048+ is recommended for RSA, 4096 for enhanced security. Ed25519 uses fixed 256 bit (comparable to 3072 bit RSA).
    - `-N`: Passphrase. Since this establishes a trusted key pair for use on trusted devices, `""` (none) allows for SSO, where device login secures ssh ability.
    - Custom keys will be saved to the following, or wherever was specified:
        - Public: `/home/<user>/.ssh/<keyPair name>.pub`
        - Private: `/home/<user>/.ssh/<keyPair name>`
- ### How to: Add key pairs to the server's authorized keys file: 
    - Type: `ssh-add --apple-use-keychain ~/.ssh/<file>`
        - `-i <path>` specifies identity file, which is optional; Else default pub key

---

# Linux/Android
- Testing on Android was done with Termux on an unrooted Android 15 Device.

## Package Management
- Check to make sure OpenSSH is installed: `apt list | grep ssh-client`
    - If not, install it with: `apt install ssh`

## General Operation

### SSH Daemon
- **Start** the Daemon on the host device: `sudo /usr/sbin/sshd` | `sudo $(which sshd)`
    - The daemon must be instantiated with admin permissions from the full path. This is for security purposes.
- **Check** to see if the daemon is running: `sudo systemctl status sshd`
- **Stop** the Daemon service: `sudo pkill sshd`

### SSH Client
- **Login** to a device: `ssh user@hostname -p 22`
    - Port specification is optional
    - For configured known hosts (~/.ssh/config): `ssh <host>`
    - Sign in with additional options: `ssh -i <path to id file> <host> -p <port>`
    - Login with verbose output for debugging: `ssh -vvv -i id_rsa <host>`
- **End** a connection: `exit`

## Configuration

### General Info
- Config Files & Keys are stored in: `/etc/ssh`
- Daemon Config: `/etc/ssh/sshd_config`
- Client Config: `/etc/ssh/ssh_config`
- Known Hosts file: `~/.ssh/known_hosts`
- #### Password
    - To check current password `passwd <user>`
    - To set password `passwd` & follow prompts

### *How To:* Configure Named Hosts (Nicknames)
- Open Client config: `sudo nano /etc/ssh/ssh_config`
- Create a new host above `Host *`; Settings are only changed once per read, subsequent changes of a variable are ignored.
- *Add a known host:*
  ```shell
  Host <arbitrary name>
    Hostname <remote host hostname/IP address>
    User <remote host username>
    Port <remote host listener port; optional>
    PubKeyAuthentication yes|no
    IdentityFile <public key filepath>
  ```
    - This allows you to login with a nickname `ssh <arbitrary name>`
    - Specifying an identity file ensures a particular public key is used & allows password-less authentication. See Configure Key Pairs (from client) for more info.
    - *Input is case sensitive
- > If there is a permissions issue, try: `chmod 600 <path to config file>`

### *How To:* Generate Key Pairs (from client)

- **REPL Guided:**
    - Type `ssh-keygen` Follow the prompts to setup a key pair.
        - Default keys should be automatically saved to:
            - Public: `~/.ssh/id_rsa.pub`
            - Private: `~/.ssh/id_rsa`

- **Specific:**
    - Type: 
    ```shell
    ssh-keygen -t <rsa|ed25519|other> -b <bits> -N <passphrase|""> -f ~/.ssh/<keyPair name>
    ```
    - `-t`: Type
    - `-b`: Encryption bits. 2048+ is recommended for RSA, 4096 for enhanced security. Ed25519 uses fixed 256 bit (comparable to 3072 bit RSA).
    - `-N`: Passphrase. Since this establishes a trusted key pair for use on trusted devices, `""` (none) allows for SSO, where device login secures ssh ability.
    - Custom keys will be saved to the following, or wherever was specified:
        - Public: `/home/<user>/.ssh/<keyPair name>.pub`
        - Private: `/home/<user>/.ssh/<keyPair name>`

### *How To:* Add key pairs to the server's authorized keys file: 
- Type: `ssh-copy-id -i <publc key path> <remote hostname>`
    - `-i <path>` specifies identity file, which is optional; Else default pub key
### *How To:* Remove old Keys
- From the client: Simply delete the files.
- From the Server: Remove each key from the `~/.ssh/authorized_keys` file

---

# Windows
- [How to SSH in WIndows official Documentation](https://learn.microsoft.com/en-us/windows/terminal/tutorials/ssh)
- [How to Configure SSH Official Windows Documentation](https://learn.microsoft.com/en-us/windows-server/administration/OpenSSH/openssh-server-configuration)

## Locations
- `C:\\Users\%user%\.ssh\`: Known hosts files & Keys
- `C:\\Users\%user%\.ssh\config`: User-specific SSH config (Read after line specified file line; Read 2)
- `C:\\programData\ssh\ssh_config`: System-wide SSH configuration files (Read 3)
- `C:\\Windows\System32\OpenSSH\`: OpenSSH Installation Folder; Executables

## Package Management
-  *How to:* Check to see if daemon is running: `services.msc` & find the sshd process in the list.
    > Still need to figure out how to make this work. When I try to start the daemon, it results in an error, I wonder if the problem is that the filepaths are either not properly confugured or that it's just not located in the proper folder. I don't have time at the moment to diagnose this, so I'll have to do so later.
- *How to:* Check to see if OpenSSH is installed as an added feature in Windows with the Windows GUI
    - Navigate to Settings > Services > Optional Features; Click 'View Features'
    - Search 'SSH' & check to see if both the SSH client & server are added as optional features. 
        - If not: Click add features; search SSH, & add them.

## General Operation

### SSH Daemon
- `ssh.exe -F <filepath>`: Specify alternate config file location
### SSH Client
- **Login** to a device: `ssh user@hostname -p 22`
    - Port specification is optional
    - For configured known hosts (~/.ssh/config): `ssh <host>`
    - Sign in with additional options: `ssh -i <path to id file> <host> -p <port>`
    - Login with verbose output for debugging: `ssh -vvv -i id_rsa <host>`
- **End** a connection: `exit`
