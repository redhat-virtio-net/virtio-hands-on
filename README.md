# Virtio Hands On
Scripts and ansible playbooks for Virtio Hands-On blog series.

The Virtio blog series are split into three threads:

 - **Solution Overview**: Intended for IT experts, it contains a high level overview of a specific architecture
 - **Deep Dive**: Intended for architects, has more technical details of how each component interacts with the rest
 - **Hands On**: Intended for developers, includes the commands needed to set up and play around with each architecture. These scripts aim to help with this task.

Here is a complete index of all the posts that comprise the blog series.


| Topic                                | Solution Overview | Deep Dive | Hands On |
|--------------------------------------|-------------------|-----------|----------|
| Introduction and vhost-net           | URL               | UL        | URL      |
| User-space networking and vhost-user | URL               | URL       | URL      |
| HW offloading                        | URL               | URL       | URL      |



For documentation on virtio and all it's related components and possible architectures, please refer to the correspondent blog post.

The aim of these set of scripts is to make it really straightforward to set up
a number of different virtio environments for testing and debugging purposes

The supported environments are:

- vhost-net
- TBD
- TBD


## Requirements
- The scripts assume you're running Fedora30 although they might work on other distros.
- Ansible > 2.6
- The ansible playbook runs locally and requires "sudo" for some tasks. When you run it,
you will be asked for the BECOME password (i.e: your sudo password)

## Vhost-net
In order to set up the vhost-net environment, run the following command:

```
    $ ansible-playbook vhost-net.yml
```
