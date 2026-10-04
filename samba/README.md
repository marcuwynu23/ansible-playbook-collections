# Samba

Samba playbook to install and configure a Samba file sharing server.  
This setup supports Debian/Ubuntu, RedHat family, SUSE, and Arch.

## How it works

```mermaid
flowchart LR
    Client([Windows/Linux Client]) -->|:445| SMB[Samba Server]
    SMB --> Disk[(Shared Storage)]
```

1. Installs Samba packages based on the detected OS family.
2. Configures `/etc/samba/smb.conf` with workgroup and security settings.
3. Creates share directories with configurable permissions.
4. Creates Samba users with passwords.
5. Enables and starts the Samba service.
6. Opens firewall ports (firewalld or ufw) if a firewall is running.

## Playbook details in this repo

- Supported OS: Debian/Ubuntu, RedHat (Fedora, RHEL, CentOS, Rocky, Alma), SUSE, Arch
- Default workgroup: `WORKGROUP`
- Default security: `user`
- Service: `smbd` (Debian), `smb` (RedHat/SUSE/Arch)
- Firewall ports: `samba` (firewalld) or `139,445/tcp` (ufw)

## Variables

| Variable | Default | Description |
|---|---|---|
| `samba_workgroup` | `WORKGROUP` | Samba workgroup name |
| `samba_server_string` | `Samba Server %v` | Server description string |
| `samba_security` | `user` | Security mode |
| `samba_map_to_guest` | `Never` | Guest access behavior |
| `samba_shares` | `[]` | List of shares to create |
| `samba_users` | `[]` | List of Samba users to create |
| `samba_manage_firewall` | `true` | Open/close firewall ports |

### Example share configuration

```yaml
samba_shares:
  - name: shared
    path: /srv/samba/shared
    valid_users: "@sambashare"
    read_only: "no"
    browsable: "yes"
```

## How to run

From the repository root:

```bash
cd samba
cp inventory.ini.example inventory.ini
ansible-playbook -i inventory.ini setup.yml
```

Remove:

```bash
ansible-playbook -i inventory.ini removal.yml
ansible-playbook -i inventory.ini removal.yml -e samba_remove_shares=true
```

Useful commands:

```bash
ansible-playbook -i inventory.ini setup.yml --check
ansible-playbook -i inventory.ini setup.yml --diff
ansible-playbook -i inventory.ini setup.yml -e samba_workgroup=MYGROUP
ansible-playbook -i inventory.ini setup.yml -e samba_shares=[{name:shared,path:/srv/samba/shared}]
```

## Notes

- Store Samba user passwords securely; the playbook uses `no_log` for user creation.
- Use `--check --diff` to preview changes before applying.
- The removal playbook keeps share directories by default; set `samba_remove_shares=true` to delete them.

## References

- Official site: <https://www.samba.org/>
- Samba documentation: <https://www.samba.org/samba/docs/>
- Debian Samba guide: <https://wiki.debian.org/Samba>
- RedHat Samba guide: <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/managing_file_systems/index>
- Arch Wiki: <https://wiki.archlinux.org/title/Samba>
