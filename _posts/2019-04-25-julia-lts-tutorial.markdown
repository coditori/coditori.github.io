---
title: "Docker containers not in depth!"
tags:
  - container
  - docker
  - moby
  - coreos
  - rkt
  - orchestration
header:
  image: /assets/images/header/containers-not-in-depth.png
toc: true
last_modified_at: 2018-01-25T18:00:40-03:30
---
We always have been using abstraction to make things more robust, reusable, and readable. Nowadays geeks use it as a term called "Container" to have a simple and fast deployment.

## Containers
A container is a runtime instance of an image. The image will be in memory when executed and runs completely isolated from the host environment, also image accesses host files and ports which configured in container config file.

## Containers vs Objects
As matter of fact a container likes an object in OOP and as you know objects need classes that to build on the top of.  
<mark>classes == images</mark>  
<mark>objects == containers</mark>

## Pack Containers
Like real-world, we can put stuff in a container and pack it, which means we can put applications and their configs inside a container that called "Containerized Application".
<figure>
	<img src="/assets/images/pack-container.jpg">
	<figcaption>Pack a container</figcaption>
</figure>

## Containers vs Virtual Machines
It's time to see the real benefits of containers rather than VMs which is:

<figure>
	<img src="/assets/images/container-vs-vm.jpg">
	<figcaption>Difference between VMs and Containers</figcaption>
</figure>

In Virtual Machine technology we need:
- Hardware Virtualization like VT-x for 64-bit guests
- A Virtual Operating System (OS) like *nix or Windows

But with Container Engine which is the under the hood technology, we don’t need above stuff. also, Container brings us:
- Faster Deployment: images are very small which reduce the deployment time 
- Version Control: any container can have a version and simply can roll back to a past version
- Portability with no depending on Operating Systems
- Sharing: use a remote repository like Git remote repo to share containers
- Simple to Maintain: applications are the same in development and production

## Docker (Moby)
As it's silly to "reinvent the wheel" we have to take a look at some available tools. When we talk about container technology we somehow refer Docker which they just changed its name to Moby but people still call it docker. The first stable release introduced in March 2013. Most of the containers listed at [Operating system level virtualization][Operating-system-level-virtualization] that you can take a look at.

## Docker Hello World
With [simple docker example][docker-simple-example] you can learn more about how to pack your application with its dependencies or making an application containerized.

## Containers disadvantages?
A container is not a complete solution for a production usage, they have following problems:
- Encapsulation: configurations will be inside the container, which means you can not put your vital data in
- Security: VM could handle vulnerabilities but container itself can not
- Management and Maintenance: it's really hard to manage many containers

As matter of fact, a container just likes a musical instrument, not an Orchestra! this means we should combine it with other technologies on Cloud or anything else. 

## Drive slow and enjoy the scenery
Yeah! we just saw how a single container can help us, in most of the real Enterprise projects we need to communicate between containers. This area is called "Orchestration" which means a bunch of containers which work together. I'll write about them later but first you need to know about containers :)

<figure>
	<img src="/assets/images/orchestration.jpg">
	<figcaption>A group of containers or Orchestration</figcaption>
</figure>

[Operating-system-level-virtualization]: https://en.wikipedia.org/wiki/Operating-system-level_virtualization#External_links
[docker-simple-example]: https://docs.docker.com/get-started/part2/#publish-the-image

