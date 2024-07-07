---
layout: post
title: 'Gnome/Keyring does not wraps around SSH Agent automatically'
date: 2024-07-07 17:14 -0300
tags: linux
---

It seems that starting from version **1:46.0-1**, the `gnome-keyring` changed it's behavior and now it disables building the `ssh-agent` support by default.

The OS by default also doesn't enable it, which was the cause for all my `ssh-agents` goes missing every time I tried to do any connection and even asking for password again.

Lookint at the Gitlab MR, it seems like we're now supposed to use `gcr-ssh-agent`. Here's the instructions:

1. **Run**: `systemctl enable --user gcr-ssh-agent.socket` to enable the socket.
2. **Set**: `$SSH_AUTH_SOCK` environment value to `$XDG_RUNTIME_DIR/gcr/ssh`. I just put this in my `.zshrc`:
	1. `export SSH_AUTH_SOCK=$XDG_RUNTIME_DIR/gcr/ssh`
3. **Reboot**

After those two changes, your SSH keys should start working just like before.

---

Other workaround I was doing before finding those threads were adding this to my `.zshrc`:

```bash
if [ -z "$SSH_AUTH_SOCK" ]; then
  eval "$(ssh-agent -s)" > /dev/null
  ssh-add ~/.ssh/id_ed25519_github &> /dev/null
  ssh-add ~/.ssh/id_ed25519_gitlab &> /dev/null
fi
```

The problem with this approach is that it will init a new ssh-agent for every newly created console session since the `.zshrc` will be loaded every time. But since it was a workaround, I didn't care. It was more stressful not to have it working at all.

### References
- [Reddit](https://www.reddit.com/r/archlinux/comments/1aq97m8/gnomekeyring_14601_in_extratesting_disables/)
- [gitlab/gnome-keyring #60](https://gitlab.gnome.org/GNOME/gnome-keyring/-/merge_requests/60)
- [Wiki/Arch Linux - Keyring#SSH_keys](https://wiki.archlinux.org/title/GNOME/Keyring#SSH_keys)
- [https://bbs.archlinux.org/viewtopic.php?pid=2152021#p2152021](https://bbs.archlinux.org/viewtopic.php?pid=2152021#p2152021)
