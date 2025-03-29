---
layout: post
title: Automatic mounting of remote storage via SSHFS on Amazon EC2 instances
date: '2014-02-19T11:04:00.004+01:00'
author: hellogetmyblogback
tags:
modified_time: '2014-02-19T11:04:56.776+01:00'
blogger_id: tag:blogger.com,1999:blog-8160351477288734008.post-2446382380226116797
blogger_orig_url: https://combichem.blogspot.com/2014/02/automatic-mounting-of-remote-storage.html
---

In this blog I demonstrate how you can create an Amazon EC2 instance image that will **automount a folder on a remote server via SSHFS**.

The purpose here is to fire up an EC2 compute server, run a program, and save the output from that program on our **local compute cluster** at the university.

Basically, you just need to add a line to `/etc/fstab` and save the instance as an image (that’s what I did).

---

### 🧰 What you need:

- An Amazon EC2 instance with **sshfs** installed.
- A user with **SSH keys properly set up** to access the remote system (the SSH keys **must not require a passphrase**).

---

Let’s say your **remote server** has a folder named `remote_folder`  
And your EC2 instance has a local mount point at `local_folder`.

Amazon EC2 Ubuntu instances typically use the `ubuntu` user, so the example assumes that.

Here’s the line to add to `/etc/fstab` (all on **one line**):

```fstab
sshfs#ubuntu@remoteserver:/home/ubuntu/remote_folder/ /home/ubuntu/local_folder/ fuse user,delay_connect,_netdev,reconnect,uid=1000,gid=1000,IdentityFile=/home/ubuntu/.ssh/id_rsa,idmap=user,allow_other,workaround=rename 0 0
```

**Notes:**
- `IdentityFile` should point to your **private SSH key**.
- `_netdev` ensures the mount happens **after the network is available**.
- `reconnect` attempts automatic reconnection.
- `delay_connect` and `workaround=rename` are often needed to avoid weird mount issues (especially on boot).

⚠️ **Don’t forget the trailing slashes** (`/`) on both folder paths — it won’t work without them (speaking from bitter experience!).

---

### 🔄 Optional: Prevent SSH disconnects

To avoid idle SSH sessions timing out, add this line to your `/etc/ssh/ssh_config`:

```ssh
ServerAliveInterval 5
```

This sends a keep-alive signal every 5 seconds.

---

That’s it!  
Simple, minimal, and works well when you're spinning up EC2 machines to crunch data and dump output to a shared server.
