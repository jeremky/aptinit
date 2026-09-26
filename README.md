# aptinit

A script that automates installing and configuring Debian/Ubuntu.

## Features

- `install_packages`: updates the system and installs the applications listed in `config/packages.cfg`

- `enable_flathub`: installs flatpak and the Flathub repository

- `enable_locate`: installs `plocate` and builds its database so it's ready to use

- `enable_unattended`: installs `unattended-upgrades` and opens its configuration tool

- `disable_tty1`: disables tty1, for SSH-only use

- `disable_sudofile`: disables the automatic creation of the `.sudo_as_admin_successful` file

- `disable_sudopasswd`: disables the password prompt for sudo commands. **DO NOT USE IN PRODUCTION!**

- `configure_ufw`: installs and configures the `ufw` firewall with the following ports:
  - 22/tcp
  - 80/tcp
  - 443/tcp


- `configure_sshd`: creates an `sshd` file (`/etc/ssh/sshd_config.d/<user>.conf`) with the following:
  - Restricts access to the main user (UID 1000)
  - Disables X11 forwarding
  - Enforces `ed25519` keys only
  - Limits authentication attempts to 3
  - Restricts algorithms to modern recommendations:
    - **Kex**: `curve25519-sha256`
    - **Ciphers**: `aes256-gcm`, `aes256-ctr`, `aes192-ctr`, `aes128-gcm`, `aes128-ctr`
    - **MACs**: `hmac-sha2-512-etm`, `hmac-sha2-256-etm`

> **Warning**: `PasswordAuthentication` stays enabled by default. Remember to disable it in `/etc/ssh/sshd_config.d/<user>.conf` after setting up your SSH keys.

## Configuration

The `config/config.cfg` file lets you configure how the script runs to suit your preferences.
Comment out the functions you don't want to use. Example:

```txt
# aptinit config

install_packages
# enable_flathub
# enable_locate
# enable_unattended

# disable_tty1
# disable_sudofile
# disable_sudopasswd

# configure_ufw
# configure_sshd
```

Alongside the config file is `config/packages.cfg`, which lists the packages to install when `install_packages` is enabled.

Example:

```txt
# aptinit packages list

curl
fail2ban
fd-find
fzf
htop
make
ncdu
net-tools
procs
ripgrep
rsync
sysstat
tree
unzip
vim
zip
zoxide
```

## Usage

Once you've edited `config/config.cfg`, run the script with root privileges:

```bash
sudo ./aptinit.sh
```
