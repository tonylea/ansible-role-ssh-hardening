# Ansible Role: SSH Hardening

An Ansible role that hardens the SSH daemon configuration by enforcing key-based authentication, strong cryptography, and restrictive access controls.

## Description

This role replaces the default `sshd_config` with a security-hardened template that follows SSH best practices. Out of the box it disables password authentication, restricts root login, enforces modern ciphers and key exchange algorithms, and turns off unnecessary features like X11 and TCP forwarding.

The role includes pre-flight assertion checks that validate every variable before touching your system, and a template validation step (`sshd -t`) that catches syntax errors before reloading the daemon.

### Supported Platforms

- Debian Trixie (13)

### Requirements

- Ansible >= 2.16

## Installation

Install from GitHub with Ansible Galaxy:

```bash
ansible-galaxy role install git+https://github.com/tonylea/ansible-role-ssh-hardening.git,v1.0.0
```

Or add to your `requirements.yml`:

```yaml
roles:
  - name: ansible-role-ssh-hardening
    src: https://github.com/tonylea/ansible-role-ssh-hardening.git
    version: v1.0.0
```

Then run:

```bash
ansible-galaxy install -r requirements.yml
```

## Usage

### Basic

Apply with all secure defaults (no root login, no password auth, strong crypto):

```yaml
- hosts: servers
  roles:
    - role: ansible-role-ssh-hardening
```

### Custom Configuration

Override any defaults to match your environment:

```yaml
- hosts: servers
  roles:
    - role: ansible-role-ssh-hardening
      vars:
        ssh_hardening_port: 2222
        ssh_hardening_allowed_users:
          - deploy
          - admin
        ssh_hardening_max_auth_tries: 3
        ssh_hardening_client_alive_interval: 600
        ssh_hardening_address_family: inet
        ssh_hardening_listen_addresses:
          - 0.0.0.0
```

### Role Variables

| Variable | Default | Description |
| --- | --- | --- |
| `ssh_hardening_port` | `22` | SSH listening port |
| `ssh_hardening_address_family` | `"any"` | Address family (`any`, `inet`, `inet6`) |
| `ssh_hardening_listen_addresses` | `["0.0.0.0", "::"]` | Addresses the daemon listens on |
| `ssh_hardening_permit_root_login` | `"no"` | Root login policy (`yes`, `no`, `prohibit-password`, `forced-commands-only`) |
| `ssh_hardening_pubkey_authentication` | `true` | Enable public key authentication |
| `ssh_hardening_password_authentication` | `false` | Enable password authentication |
| `ssh_hardening_permit_empty_passwords` | `false` | Allow empty passwords |
| `ssh_hardening_challenge_response_authentication` | `false` | Enable challenge-response authentication |
| `ssh_hardening_use_pam` | `true` | Enable PAM |
| `ssh_hardening_allowed_users` | `[]` | Users allowed to connect (empty = no restriction) |
| `ssh_hardening_allowed_groups` | `[]` | Groups allowed to connect |
| `ssh_hardening_denied_users` | `[]` | Users denied access |
| `ssh_hardening_denied_groups` | `[]` | Groups denied access |
| `ssh_hardening_login_grace_time` | `60` | Seconds before login timeout |
| `ssh_hardening_max_auth_tries` | `4` | Max authentication attempts |
| `ssh_hardening_max_sessions` | `10` | Max concurrent sessions |
| `ssh_hardening_client_alive_interval` | `300` | Seconds between keepalive messages |
| `ssh_hardening_client_alive_count_max` | `3` | Missed keepalives before disconnect |
| `ssh_hardening_allow_agent_forwarding` | `false` | Enable SSH agent forwarding |
| `ssh_hardening_allow_tcp_forwarding` | `false` | Enable TCP forwarding |
| `ssh_hardening_x11_forwarding` | `false` | Enable X11 forwarding |
| `ssh_hardening_log_level` | `"VERBOSE"` | Logging verbosity |
| `ssh_hardening_print_motd` | `false` | Print message of the day |
| `ssh_hardening_use_dns` | `false` | DNS lookup for connecting hosts |
| `ssh_hardening_banner` | `"none"` | Path to banner file |
| `ssh_hardening_ciphers` | See [defaults/main.yml](defaults/main.yml) | Allowed ciphers |
| `ssh_hardening_kex_algorithms` | See [defaults/main.yml](defaults/main.yml) | Key exchange algorithms |
| `ssh_hardening_macs` | See [defaults/main.yml](defaults/main.yml) | Message authentication codes |
| `ssh_hardening_host_key_algorithms` | See [defaults/main.yml](defaults/main.yml) | Host key algorithms |

## Tests

This role uses [Molecule](https://ansible.readthedocs.io/projects/molecule/) with Docker for integration testing. Tests verify config file contents, file permissions, and live daemon state. Both default and custom-variable scenarios are tested with idempotency checks.

```bash
pip install -r requirements-test.txt
molecule test
molecule test -s custom-vars
```

## Credits

- [Tony Lea](https://github.com/tonylea)

## License

[MIT](LICENSE)
