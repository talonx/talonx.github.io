---           
layout: post
title: Software Defined Networking - A Short Introduction
date: 2024-04-19 00:00:00 IST
comments: true
categories: softwaredefinednetworking computernetworking
---


Programmable Networks
---------------------

Let's take a simple example. To set up a simple network switch for your home network, you have to connect it using LAN cables to your router, and then to the individual 
devices - laptops, desktops, your NAS, and so on. You "define" the routes between the devices using cables and sockets. Switches are "smart" in the sense they learn routes 
to the devices connected to them. You cannot change the way the routes are defined - your switch has 6 or 8 or more ports which it knows about, and how to route packets between the devices. The routing logic is hardcoded in the device.

But what if you wanted to change the routing logic to something custom? And what if you had multiple switches, and you wanted to control the routing logic from one place? Traditional networking devices won't allow you to do this.

Why Should You, an Ops Engineer, Know Anything About SDN?
---------------------------------------------------------

The entire foundation of a cloud is based on the virtualization of resources - storage, compute, and network. The concepts of SDN make it easier to enable network virtualization. 
Clouds are fundamentally multi-tenanted, and having an idea of how things work behind the scenes can give us a better appreciation of what goes on under the hood. As a cloud user or admin,
you don't need to know how SDN works because it's never exposed to you. Nevertheless, it can be fascinating especially if you are interested in networking, like I am.

The Control Plane and the Data Plane
------------------------------------

Before going ahead we need to understand these two terms. There are two primary functions where data transfer is concerned - transferring the data itself and communicating the routing 
messages that define how the data should be transferred.

**Control plane** - The part of a networking infrastructure that decides how to handle traffic.

**Data plane** - The part of a networking infrastructure that forwards traffic according to the control plane's decisions.

In addition, there is a "controller" which sits on top of the control plane and is the gateway to configure the data plane devices. A single software controller can control multiple data planes using APIs.

A Bit of History
----------------

Networking equipment used to be configurable using vendor-specific interfaces on individual devices. The evolution of SDN was driven by several factors, and happened in roughly three stages, a
s described in Feamster et al's paper [1].

#### **Active networks**

Active networks were an initiative where network devices (we will refer to each one as a node) exposed internal resources using a network API. Packets passing through a node could undergo custom p
rocessing (in the data plane). This was born out of frustration with long timeframes to deploy new network services, the difficulty of customizability for specific applications, and researchers' desire for the ability to experiment at a large scale.

#### **Control and data plane separation**

As commodity hardware became cheaper and more powerful, and ISPs had to manage larger networks, research projects started to focus on the control plane, rather than data plane (which was the 
case in active networking) programmability. Visibility into the entire network also became a requirement. There was some initial skepticism around *not* having a single point of failure (e.g. 
the control plane failing and the data plane continuing to work) but similar problems already existed in existing hardware.

#### **The OpenFlow API**

OpenFlow is a protocol specification of the data plane functionality and also a protocol between the controllers and the data plane devices in a setup where these two planes were separated.
OpenFlow allowed packet forwarding rules to be defined on much more than the destination IP address, which was the case with traditional devices.

So what identifies a network as software-defined?

-   Separate control and data planes.

-   The control plane is a centralized controller or set of controllers that can view and control the entire network or networks and is implemented as software that can run on commodity hardware.

-   Data plane devices are "dumb" forwarding devices.

-   Well-known (public) interfaces exist between the control plane devices (controllers) and the data plane devices).

-   Other software can program the network using the SDN controllers to suit their needs.

The OpenFlow Protocol
---------------------

The initial OpenFlow spec was born out of the idea that the already existing flow tables (or access control lists) in network devices could be used to describe newer packet forwarding behaviour [2].
There was also a need to divide ("slice") the flow tables so that researchers could run experiments on production networks without impacting real-world traffic. 

After separating the planes, admins could program the control plane from any operating system remotely, and thus define the flow tables the way they wanted without programming the device itself.

Virtualizing the Network
------------------------

Network virtualization (NV) allows one or more virtual networks to exist on top of a shared physical network. Virtual networks predate the idea of SDN. The concept is related to SDN because
SDN enables NV more easily. 

If you use any cloud provider, you already use network virtualization. The VPC in AWS, or in GCP - are just virtual (and your personal) networks built on top of shared physical ones.

Setup a Virtual Network on Your Own
-----------------------------------

[Mininet](https://mininet.org/){:target="_blank"} is a software that can create a virtual network or networks on a single machine, letting you play around with various network topologies on your 
laptop. The network devices are emulated in software.

You can run Mininet

-   From the CLI

-   Programmatically

-   Using miniedit - a rudimentary GUI. I don't recommend this as it's easy to trip on some bugs.

We'll take the programmatic approach.

Installing Mininet is simple using your package manager if you're on Linux.

On Debian-based distros, run

```
sudo apt install mininet
```

Although you can create complex topologies (Ring, Tree, and so on), we will create simple host and switch-based ones here. You can refer to the [Mininet docs](http://mininet.org/api/annotated.html){:target="_blank"} for more information.

### **A Simple Two-Host Network Topology**

Source: <https://github.com/talonx/mininet-demo/blob/main/basic_topology.py>

```
from mininet.topo import Topo, LinearTopo;
from mininet.net import Mininet;
from mininet.cli import CLI;

class Basic(Topo):

    def __init__(self):
        Topo.__init__(self)

        h1 = self.addHost("h1");
        h2 = self.addHost("h2");

        s1 = self.addSwitch("s1");

        self.addLink(h1, s1, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);
        self.addLink(h2, s1, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);

topo = Basic();
net = Mininet(topo=topo) # Uses the default reference controller
net.start()
CLI(net)
net.stop()
```

This defines two hosts h1 and h2 and a switch connecting them using the addLink method. This forms the core of any Mininet topology definition.

There are different ways to start the network simulator, but the easiest is to invoke

```
net.start()
```

and then move into interactive mode with

```
CLI(net)
```

which will drop you into a command prompt (mininet>) where you can run various commands to interact with your virtual network.

Run the Python script by invoking

```
sudo python3 basic_topology_run_cmd.py
```

The dump command shows you the hosts and devices

```
mininet> dump
<Host h1: h1-eth0:10.0.0.1 pid=885505>
<Host h2: h2-eth0:10.0.0.2 pid=885507>
<OVSSwitch s1: lo:127.0.0.1,s1-eth1:None,s1-eth2:None pid=885512>
<OVSController c0: 127.0.0.1:6653 pid=885498>
```

Note that Mininet created a default SDN controller since we did not provide one. This is sufficient for simple topologies.

Now try pinging h2 from h1

```
mininet> h1 ping h2 -c 3
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=4.45 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=0.280 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=0.081 ms

--- 10.0.0.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2023ms
rtt min/avg/max/mdev = 0.081/1.602/4.445/2.011 ms
```

Type quit or exit to exit from the CLI.

### **Running Commands Inside the "Hosts"**

Source: <https://github.com/talonx/mininet-demo/blob/main/basic_topology_run_cmd.py>

```
from mininet.topo import Topo, LinearTopo;
from mininet.net import Mininet;
from mininet.cli import CLI;

class Basic(Topo):

    def __init__(self):
        Topo.__init__(self)

        h1 = self.addHost("h1");
        h2 = self.addHost("h2");

        s1 = self.addSwitch("s1");

        self.addLink(h1, s1, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);
        self.addLink(h2, s1, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);

topo = Basic();
net = Mininet(topo=topo) # Uses the default reference controller
net.start()

h1 = net.get("h1");
res = h1.cmd("route -n")
print(res)

net.stop()
```

This illustrates running a command from inside one of the hosts.

### **A Multi-Switch Topology**

Source: <https://github.com/talonx/mininet-demo/blob/main/multi_switch.py>

```
from mininet.topo import Topo, LinearTopo;
from mininet.net import Mininet;
from mininet.cli import CLI;

class Lan(Topo):

    def __init__(self):
        Topo.__init__(self)

        s1 = self.addSwitch("s1");
        s2 = self.addSwitch("s2");
        h1 = self.addHost("h1");
        h2 = self.addHost("h2");

        # Link host 1 to switch 1
        self.addLink(h1, s1, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);
        # Link host 2 to switch 2
        self.addLink(h2, s2, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);
        # Link switch 1 to switch 2
        self.addLink(s1, s2, bw=1, delay="10ms", loss=0, max_queue_size=1000, use_htb=True);

topo = Lan();
net = Mininet(topo=topo) # Uses the default reference controller
net.start()
CLI(net)
net.stop()
```

This illustrates two switches, with one host connected to each. Check the links created once you're inside the CLI

```
mininet> links
h1-eth0<->s1-eth1 (OK OK)
h2-eth0<->s2-eth1 (OK OK)
s1-eth2<->s2-eth2 (OK OK)
```

And then ping h2 from h1 (they are not connected to the same switch)

```
mininet> h1 ping h2 -c 3
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=0.077 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=0.077 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=0.080 ms

--- 10.0.0.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2025ms
rtt min/avg/max/mdev = 0.077/0.078/0.080/0.001 ms
```

Mininet uses the reference implementation of an SDN controller if you don't specify anything.

There are other software controllers available

1.  Pox - <https://github.com/noxrepo/pox>

2.  Ryu - <https://ryu-sdn.org/>

3.  ONOS - <https://opennetworking.org/onos/>

4.  OpenVSwitch - <https://www.openvswitch.org/>

5.  OpenDaylight - <https://www.opendaylight.org/>

#### References

1.  The Road to SDN - An intellectual history of programmable networks - <https://queue.acm.org/detail.cfm?id=2560327>

2.  Cloud Native Data Center Networking: Architecture, Protocols, and Tools - Dinesh G. Dutt - <https://www.oreilly.com/library/view/cloud-native-data/9781492045595/>

3.  Foundations of Modern Networking - William Stallings - <https://www.oreilly.com/library/view/foundations-of-modern/9780134175478/>

Thank you for reading, and do reach out via comments or on [Twitter](https://twitter.com/talonx){:target="_blank"} if you want to chat or share your thoughts.