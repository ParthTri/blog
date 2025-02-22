---
draft: "false"
title: The Hunt for a Simple DNS Resolver
date: 2025-02-22
---
Lately I've been finding the need to add host names to the services I host on my home server. The usual straightforward way of doing this would be just adding static routes directly through your router (if you use it for DNS and DHCP handling). However I unfortunately have a router that doesn't support this currently, so my next option was setting up a separate service that will resolve IP address to whatever I want.

### Requirements
This created the following list of requirements for the service I would like:
- [ ] **Open source** - I don't want to be paying for just hosting DNS records for my network
- [ ] **Easy to setup** - Ideally an application that I can spin up using docker or on quickly install on a Linux container such as an Alpine LXC
- [ ] **Easy to configure** - A reasonably easy way of configuring the records. I probably won't be updating the records often so even some simple configuration wouldn't be too bad.
- [ ] **Fast** - As I have quite a few people that I would describe as heavy users of the Internet, the DNS resolving should be fast as not to slow down workflows

### tldr
For those who don't want to bore themselves with the entire post and the hunt, I ended up creating my own DNS resolver that I will try out but don't recommend using it yourself as I can't guarantee it working correctly 100% of the time.

Check it out [here](https://github.com/ParthTri/Simple-DNS).

### Awesome Selfhosted

Now being an amateur sysadmin I first turned my sights on [Awesome Selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted), a collection of self-hostable solutions to common software. Going down the list of awesome projects created by awesome authors, I went through the list of DNS re-solvers.

Here's my quick comparison of the list against my criteria and what I could tell based on my (admittedly little) research.

| Software   | Opensource | Easy Setup | Easy Config | Fast |
| ---------- | ---------- | ---------- | ----------- | ---- |
| AdGuard    | X          | X          | X           | X    |
| PiHole     | X          | -          | -           | X    |
| Technitium | X          | X          | X           | X    |

Pihole seemed to have some issues with installing however I did find that there was a docker image that I could use. Out of the box however it didn't seem to support adding DNS records unless `unbound` was installed. Might have to revisit this another time.
### Choosing

Now out of all those options AdGuard and Technitium seemed like the best options, which I still would recommend anyone to try. I have still yet to try one of them out, but after surfing a few sub-reddits AdGuard maybe the way for me since it does also offer ad filtering and other benefits.

### Simple DNS

Ultimately since my needs where purely, I want the IP address of this host in my network based an arbitrary name it didn't have to be that complicated. This lead to the thought *I'm a developer. Why not build by own DNS resolver*. And that's what [Simple DNS](https://github.com/ParthTri/Simple-DNS) is mean't to be. An easy to host and setup DNS resolver, that is easy to configure.

And so began the development of a new project. I started off very optimistic, thinking I will write this the Go way, and barely rely on external libraries. That sentiment not very long, and I quickly found myself using packages to make life easier.

I used the amazing DNS library [github.com/miekg/dns](https://pkg.go.dev/github.com/miekg/dns@v1.1.63), an excellent library that was well documented and worked like a charm. Then I made use of the `go-yaml` a well known library for working with yaml files. Knowing that I wanted configuration to be minimal I reached for yaml as it easy to edit, especially as I can do it just be SSHing into the server and editing the files.

#### The run down
Now to not bore you too much. Here's how it works
1. Request comes in and is met by handler
2. Handler checks if the request is in the cache
	1. Return  the value from cache if it is
3. Check if the base domain name is in the configuration i.e. `test.parth.com` check if `parth.com` is in the configuration files
	1. Return the value if it is
4. Pass the query to the next upstream resolver
	1. If there is an answer store it in cache
	2. Otherwise use the next resolver until an answer is found
5. Return it to the end user

## Con.

I do not recommend running Simple-DNS in your own LAN just use something that is battle tested.

The process of building my own resolver was quite enjoyable. I'll be working on this on the side on-and-off adding features and testing to see if it's worth using but the features, usability, and credibility of the other resolvers is much better than mine.