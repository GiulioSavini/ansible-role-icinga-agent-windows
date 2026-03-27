# Ansible Role: Icinga Agent for Windows

Install and configure the Icinga monitoring agent on Windows hosts via OpenSSH, using the [Icinga PowerShell Framework](https://icinga.com/docs/icinga-for-windows/latest/) and Director Self-Service registration.

## Requirements

- Windows hosts with **OpenSSH** configured (user + SSH key)
- Ansible `ansible.windows` collection (`>= 2.1.0`)
- PowerShell 5.1+ on target hosts
- Network access from Windows hosts to the NetEye/Icinga Director URL

## Role Variables

| Variable | Required | Default | Description |
|---|---|---|---|
| `icinga_director_url` | yes | `""` | NetEye/Icinga Director URL |
| `icinga_self_service_api_key` | **yes** | `""` | Self-Service API key from the Director host template |
| `icinga_use_director_self_service` | no | `1` | Enable Director Self-Service registration |
| `icinga_override_director_vars` | no | `0` | Override Director variables on reconfigure |
| `icinga_reconfigure` | no | `true` | Reconfigure an existing agent |

## Example Playbook

```yaml
- hosts: windows_servers
  vars:
    ansible_connection: ssh
    ansible_shell_type: powershell
  roles:
    - role: giuliosavini.icinga_agent_windows
      icinga_director_url: "https://your-neteye-host/neteye/director/"
      icinga_self_service_api_key: "your-api-key-from-director-template"
```

## Inventory Example

```ini
[windows_servers]
win-server-01 ansible_host=192.168.1.10
win-server-02 ansible_host=192.168.1.11

[windows_servers:vars]
ansible_connection=ssh
ansible_shell_type=powershell
ansible_user=administrator
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

## What It Does

1. Trusts PSGallery repository (avoids interactive prompts)
2. Installs the `icinga-powershell-framework` PowerShell module
3. Runs `Use-Icinga` and `Start-IcingaAgentInstallWizard` with your Director URL and API key
4. Ensures the `icinga2` Windows service is running and set to auto-start

All steps run as `SYSTEM` to satisfy the admin requirement, and all interactive prompts are suppressed via `-Force` and `$ConfirmPreference = 'None'`.

## Publishing to Ansible Galaxy

```bash
# Login
ansible-galaxy login --github-token YOUR_TOKEN

# Import (uses meta/main.yml for naming)
ansible-galaxy role import GiulioSavini ansible-role-icinga-agent-windows
```

## License

MIT
