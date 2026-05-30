# aptinstall

Script automatisant l'installation et le paramétrage de Debian/Ubuntu.

Le script dispose désormais d'une séparation entre un mode `server` et un mode `desktop`.

## Fonctionnalités

- `install_packages` : met à jour le système et installe les applications présentes dans le fichier `config/<mode>/packages.cfg`

- `enable_flathub` : installe flatpak et le repo flathub

- `enable_locate` : installe `plocate` et crée la base pour l'utiliser directement

- `enable_unattended` : installe `unattended-upgrades` et vous ouvre l'outil de configuration

- `disable_tty1` : désactive le tty1 si c'est pour une utilisation uniquement par SSH

- `disable_sudofile` : désactive la création automatique du fichier `.sudo_as_admin_successful`

- `disable_sudopasswd` : désactive la demande du mot de passe pour les commandes sudo. **A NE PAS UTILISER EN PROD !**

- `configure_ufw` : installe et configure le firewall `ufw` avec les ports suivants :
  - 22/tcp
  - 80/tcp
  - 443/tcp

- `configure_sshd` : crée un fichier pour `sshd` (`/etc/ssh/sshd_config.d/<user>.conf`) avec les éléments suivants :
  - Restreint l'accès à l'utilisateur principal (UID 1000)
  - Désactive le forwarding X11
  - Force l'utilisation de la clé `ed25519` uniquement
  - Limite les tentatives d'authentification à 3
  - Restreint les algorithmes aux recommandations modernes :
    - **Kex** : `curve25519-sha256`
    - **Ciphers** : `aes256-gcm`, `aes256-ctr`, `aes192-ctr`, `aes128-gcm`, `aes128-ctr`
    - **MACs** : `hmac-sha2-512-etm`, `hmac-sha2-256-etm`

> **Attention** : `PasswordAuthentication` reste activé par défaut. Penser à le désactiver dans `/etc/ssh/sshd_config.d/<user>.conf` après avoir configuré les clés SSH.

## Configuration

Un fichier de configuration sous `config/<mode>/config.cfg` permet de paramétrer l'exécution du script selon vos préférences.
Commentez les fonctions que vous ne voulez pas utiliser. Exemple avec le mode `server` :

```txt
# aptinstall server config

install_packages

# enable_flathub
enable_locate
enable_unattended

disable_tty1
disable_sudofile
# disable_sudopasswd

configure_fail2ban
# configure_ufw
configure_sshd
```

Avec le fichier de config se trouve un fichier contenant la liste des paquets à installer si `install_packages` est actif.

Exemple avec le fichier `config/server/packages.cfg` :

```txt
# aptinstall server list

colordiff
curl
duf
fail2ban
fd-find
fzf
git
htop
make
ncdu
net-tools
pipes-sh
ripgrep
rsync
shellcheck
shfmt
ssh-audit
sysstat
tree
tty-clock
unzip
vim
zip
zoxide
```

## Utilisation

Une fois le fichier `config/<mode>/config.cfg` modifié, lancez le script avec les droits root :

```bash
sudo ./aptinstall.sh <mode>
```
