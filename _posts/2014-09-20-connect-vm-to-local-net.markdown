---
layout: post
title:  "Connect a VM into a local network"
date:   2014-09-20 20:31:00 -0300
categories: command-line terminal
---
A Virtual Machine (VM) can be connected into the local network as another machine connected to the router. To do it, I will mention the steps for VirtualBox but the same can be achieved for other products.

Go to Settings -> Network and enable a new and single Network Adapter, attached to "Bridged Adapter" in the interface `eth0`.

![menu Settings Network](http://i.imgur.com/UYj8F9n.png)

Now make sure that the VM is using DHCP. Take a look at the file `/etc/network/interface` and it should have the `eth0` interface as autostart and DHCP configured.

![file /etc/network/interface](http://i.imgur.com/28CTWKg.png)

The next time you boot the machine, `ifconfig` will show you an address like 192.168.x.x in `eth0`

![command ifconfig](http://i.imgur.com/3oL8JeC.png)

More information about VM networking at the [VirtualBox manual][vm-manual].

Resource: http://catlingmindswipe.blogspot.com.ar/2012/06/how-to-virtualbox-networking-part-two.html

[vm-manual]: http://www.virtualbox.org/manual/ch06.html#network_bridged
