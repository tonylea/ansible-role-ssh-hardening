# ssh_hardening

Allows SSH logins only with keys, and never as root:

- Key logins on; password, keyboard-interactive and empty-password logins off.
- Root can't log in, even with a key.

The settings go in `/etc/ssh/sshd_config.d/00-hardening.conf`. sshd uses the first value it reads, and reads drop-ins
in name order, so this file overrides any other drop-in, such as cloud-init's `50-cloud-init.conf`. It is checked with
`sshd -t` before sshd is reloaded.

The role stops before changing anything if the login user has no authorized key, so it can't lock them out.

Tested on Debian trixie (Raspberry Pi OS).

## Variables

| Variable | Default |
| --- | --- |
| `ssh_hardening_login_user` | `ansible_user` |

## Usage

```yaml
- hosts: k3s_cluster
  become: true
  roles:
    - ssh_hardening
```

## Development

Tests run with Molecule in a Debian trixie container running systemd and sshd, set up as Raspberry Pi Imager leaves a
Pi, with cloud-init's drop-in switching password logins on. They log in over SSH: only key logins are offered, a key
login works, root is refused even with a valid key, and the role stops for a user with no key.

```sh
pip install -r requirements-test.txt
molecule test
```

Commits follow Conventional Commits. Releases are versioned and tagged automatically from `main`.
