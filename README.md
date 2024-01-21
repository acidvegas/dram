# Decentralized Remotely Accessed Memory

Ever joked about [downloading more RAM](https://downloadmoreram.com/)? 🤣 WELL NOW YOU CAN MOTHER FUCKER!

**Here's the deal:** we use a [tmpfs](https://en.wikipedia.org/wiki/Tmpfs) on the donor machine to pretend a chunk of its RAM is a hard drive. Then, we get fancy with [NFS](https://en.wikipedia.org/wiki/Network_File_System) to share this faux-drive over the network 🌐. On the recipient's side, we mount this networked RAM-drive and set up a swap file.

Go insane with it & donate RAM from multiple machines...decentralize the RAM of a single server all over the planet! Latency is the devil 😈 LOL

## The make-it-happen Magic
#### Setup RAM Donator
1. Create an entry in `/etc/fstab` to create the RAM partition:

`tmpfs /mnt/ramote_send tmpfs nodev,nosuid,noexec,nodiratime,size=1G 0 0`

**Note:** Change `1G` to the amount of RAM you want to donate.

2. Create the RAM partition directory & mount it:

```bash
mkdir -p /mnt/ramote_send
mount /mnt/ramote_send
```

3. Download & install an NFS server daemon of your choice:

```bash
apt-get install nfs-kernel-server
```

4. Configure & start the NFS server:
- Edit your `/etc/exports` file:

`/mnt/ramote_send <client-ip>(rw,sync,no_root_squash,no_subtree_check)`

- Start & enable the service:

```bash
systemctl start nfs-kernel-server
systemctl enable nfs-kernel-server
```

---

#### Setup RAM Receiver
1. Create a directory & mount the NFS:

```bash
mkdir -p /mnt/ramote_recv
mount <nfs-server-ip>:/mnt/ramote_send /mnt/ramote_recv
```

2. Create & enable a swap file inside the NFS directory:
```bash
dd if=/dev/zero of=/mnt/ramote_recv/swapfile bs=1M count=1024
chmod 600 /mnt/ramote_recv/swapfile
mkswap /mnt/ramote_recv/swapfile
swapon /mnt/ramote_recv/swapfile
```

**Note:** Change the swap file size according to what you allocated on the donator.

3. Create an entry in `/etc/fstab` to for the NFS mount & the swap file:

```
<nfs-server-ip>:/mnt/ramote_send /mnt/ramote_recv nfs defaults 0 0
/mnt/ramote_recv/swapfile swap swap defaults 0 0
```

You can do this across various machines & use multiple swap files for decentralize even more.

___

###### Mirrors for this repository: [acid.vegas](https://git.acid.vegas/dram) • [SuperNETs](https://git.supernets.org/acidvegas/dram) • [GitHub](https://github.com/acidvegas/dram) • [GitLab](https://gitlab.com/acidvegas/dram) • [Codeberg](https://codeberg.org/acidvegas/dram)
