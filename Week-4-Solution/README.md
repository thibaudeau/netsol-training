# Week 3 Data Model Solution

I have setup the lab with 5 routers, 2 hubs and 3 spokes. Each spoke is a branch, and each hub is a small Datacenter. I kinda mixed the idea of services and devices in the data model, so it is a bit messy. I am honestly having some trouble coming up with a service that would make it applicable to my current work so it ended up being a "branch" service, which I know is not very sophisticated.

My jinja2 was not very good before this exercise and this helped a lot, despite the lack of service abstraction I came up with.

## Business Logic (implied data)

* Loopback ip range: 172.16.16.0/24
* All point-to-point links are 169.254.254.x/29
* BGP AS# are derived to be in 65100-65199 range (implies I wouldn't have anymore than 100 sites)
* I set it up so that all branch eth0/1 are connected to Hub-1 and eth0/2 to hub-2 so there is no interface name per se in the data model, only hub interfaces (left.)

## host_vars:
The following variables are configured in the host_vars

hostname:
os: OS to install with Napalm

Hub host_vars also have "server subnets" that are just loopbacks in this case that provide some more routes. (I probably should move those to a service but I ran out of time)

## group_vars

### all.yml

* SNMP Configurations
* NTP Configurations
* DNS

## roles:

### fabric
I initially had the fabric.yml in a vars folder in the fabric role, but I moved it to the root folder of the project for visibility.

roles/fabric/templates/ios/fabric.j2 has my template for the fabric. It is a bit of a combination of what David Barroso had in the Abstract Everything and what Dinesh Dutt had in the DC fabric. I tried to make the data model as simple as possible and to derive everything from the host IDs.

### branch
Branch-specific configurations: LAN, VOIP , ATMs, etc..

### hub
Hub-specific configurations: OSPF, Server VLANs, etc...

### base

Base configuration, using what is in the all.yml group_vars file. I managed to find an easy way to do wildcard masks in there and I am happy about it (I think you might have covered that at some point but I couldn't find it.)

## Results

I generate the configs with the create-config.yml playbook and they are stored into the compiled folder.
