# Svr1

Setup code for my homeserver.

## SSH Config

```bash
Host svr1-local
    HostName # local ipv4 of server
    User root
    IdentityFile ~/.ssh/ssh_bl4ckspell@proton.me
    IdentitiesOnly yes

Host svr1-public-ipv4
    HostName # public ipv4 of router
    User root
    IdentityFile ~/.ssh/ssh_bl4ckspell@proton.me
    IdentitiesOnly yes

Host svr1-public-ipv6
    HostName # public ipv6 of router
    User root
    IdentityFile ~/.ssh/ssh_bl4ckspell@proton.me
    IdentitiesOnly yes
```

## Installation Steps

### Debian Install _(on server)_

- Leave root password empty.

- Partitions:

  - ESP: 500MB
  - boot: ext4 1.5GB
  - root: luks + lvm ext4

- GRUB Bootloader
- GNOME Desktop Environment
- SSH Server

### Grub _(on server)_

Mount ESP to `/efi` instead of `/boot/efi`:

1. Open "Disks", edit ESP Partition Mount Options.
2. `sudo grub-install --efi-directory=/efi`
3. `sudo update-grub`

### Roll out initial ssh key _(from remote)_

Login to user via `ssh user@IP` and password.

```bash
sudo -i
```

```bash
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKu1vcd8RLWKmfSJlb/i4HXmhs34T+exkmIEWx2yX+C7 bl4ckspell@proton.me" >> /root/.ssh/authorized_keys
```

```bash
chmod 600 /root/.ssh/authorized_keys
chown -R root:root /root/.ssh
```

### APT _(from remote)_

```bash
apt modernize-sources
```

### Automatic LUKS Decryption _(on server/from remote)_

Save your key to disk:

```bash
sudo -i
```

```bash
echo "my-very-long-passphrase" > /opt/luks.key
```

### Run Ansible Playbook _(from remote)_

Finally, run the playbook:

```bash
ansible-playbook -i inventory.yml playbook.yml [--tags tags]
```

### Cursor Theme

Manually set cursor theme using:

```bash
gsettings set org.gnome.desktop.interface cursor-theme Vimix-cursors
```

## Router Settings

- always assign same ipv4 address
- enable wake on lan
- enable port forwarding for port 22 (ssh)
