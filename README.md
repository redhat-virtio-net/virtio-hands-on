# Virtio Hands On
Scripts and ansible playbooks for Virtio Hands-On blog series.

The Virtio blog series are split into three threads:

 - **Solution Overview**: Intended for IT experts, it contains a high level overview of a specific architecture
 - **Deep Dive**: Intended for architects, has more technical details of how each component interacts with the rest
 - **Hands On**: Intended for developers, includes the commands needed to set up and play around with each architecture. These scripts aim to help with this task.

Here is a complete index of all the posts that comprise the blog series.

For a high level introduction of what is virtio technology refer to [the introduction blog](https://www.redhat.com/en/blog/introducing-virtio-networking-combining-virtualization-and-networking-modern-it).


| Topic                                | Solution Overview | Deep Dive | Hands On |
|--------------------------------------|-------------------|-----------|----------|
| **vhost-net**           | [Introduction to virtio-networking and vhost-net](https://www.redhat.com/en/blog/introduction-virtio-networking-and-vhost-net)               | [Deep dive into Virtio-networking and vhost-net](https://www.redhat.com/en/blog/deep-dive-virtio-networking-and-vhost-net) | [Hands on vhost-net: Do. Or do not. There is no try](https://www.redhat.com/en/blog/hands-vhost-net-do-or-do-not-there-no-try)  | 
| **vhost-user** | [How vhost-user came into being: Virtio-networking and DPDK](https://www.redhat.com/en/blog/how-vhost-user-came-being-virtio-networking-and-dpdk) | [A journey to the vhost-users realm](https://www.redhat.com/en/blog/journey-vhost-users-realm)       | [Hands on vhost-user: A warm welcome to DPDK ](https://www.redhat.com/en/blog/hands-vhost-user-warm-welcome-dpdk) |
| **HW offloading** | pending               | pending       | pending      |



For documentation on virtio and all it's related components and possible architectures, please refer to the correspondent blog post.

The aim of these set of scripts is to make it really straightforward to set up
a number of different virtio environments for testing and debugging purposes

The supported environments are:

- vhost-net
- vhost-user
- TBD


## Requirements
- The scripts assume you're running Fedora30 although they might work on other distros.
- Ansible > 2.6
- The ansible playbook runs locally and requires "sudo" for some tasks. When you run it,
you will be asked for the BECOME password (i.e: your sudo password)

## Vhost-net
In order to set up the vhost-net environment, first you should check the settings file that can be found at `vars/vhost-net_settings.yml`.
Customize any variable you consider necessary (for example, the guest's root password).

Then, run the following command:

```
    $ ansible-playbook vhost-net.yml
```

## Vhost-user
### Prerequisites
In order to set up the vhost-user environment, first you should check the settings file that can be found at `vars/vhost-user_settings.yml`.
Customize any variable you consider necessary (for example, the guest's root password).

We need to check if your processor supports some features: ssse3 instructions and
hugepage. You can check it in `/proc/cpuinfo`:

```
$ grep '^flags' /proc/cpuinfo | uniq
flags           : [...] pdpe1gb [...] ssse3 [...]
```

If you don't see them in `/proc/cpuinfo` output, you still can test the
described environment, but you will need either to use the 2M pages (if you
don't see pdpe1gb but your processor still supports 2m ones) or recompile dpdk.
Both options are, currently, out of this script.

### Hugepages

Note: ansible script is able to perform this steps automatically, so you only
need to read this section if you are interested in do it manually.

Modify your kernel's command line parameters to enable 1G hugepage. The
following command will add 6 hugepages of size 1G:

```
grubby --args='default_hugepagesz=1G hugepagesz=1G hugepages=6' --update-kernel $(grubby --default-kernel)
```

A reboot is needed for the command line options to take effect. You can check
that the change has been applied in `/proc/cmdline` and `/proc/meminfo`:

```
$ grep huge /proc/cmdline
[...] default_hugepagesz=1G hugepagesz=1G hugepages=6
$ grep DirectMap1G /proc/meminfo
DirectMap1G:    14680064 kB
```

### Run the script
Finaly, run the following command:

```
    $ ansible-playbook vhost-user.yml
```

The ansible script will:

- Set up a virtual machine running Centos 7
- Configure two vhost-user interfaces on it
- Run DPDK's testpmd application on the host. The application is interactive so
it will open inside a tmux session
- Start the guest and configure hugepages on it
- Start DPDK's testpmd application on the guest as well (also inside a tmux session)

After running the playbook you can:
- Inspect the host's testpmd application by running:

```
$ tmux attach-session -t testpmd-session
```

- Jump into the guest by running:

```
$ export LIBVIRT_DEFAULT_URI="qemu:///system"; virsh command vhuser-test1
```

- Inspect the guest's testpmd application by running:

```
$ tmux attach-session -t guest-testpmd-session
```

