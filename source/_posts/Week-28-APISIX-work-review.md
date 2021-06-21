---
title: Week 28 APISIX work review
date: 2020-07-11 22:19:29
tags: [APISIX, OpenSource]
---



# lua-resty-etcd environment configuration

Waste so much time but only find have a look at **travis.yml** is enough...

Basically needs three parts：

1. Download etcd and etcdctl from github official repo
2. Install Openresty and luarocks from apt source
3. Deploy goreman as daemon as run multiple etcd node

And what I did but did it wrong is including:

1. Download Lua, Openresty and Nginx seperately, and found I could not get variable 'ngx' in lua
2. Install quite a lot of different versions of lua/luajit and make system path mess up
3. Spend quite a lot of time to support systemd in WSL2, but testing on `cluster.t` would still failed. (I guess the reason is not related to daemon but didn't add auth)

**READ CI WHEN YOU CONFIGURE YOUR ENVIRONMENT!**

---

# Add new client API

The main reference is this one - [rpc.swagger.json](https://github.com/etcd-io/etcd/blob/master/Documentation/dev-guide/apispec/swagger/rpc.swagger.json), the swagger generated API, and [API reference](https://github.com/etcd-io/etcd/blob/master/Documentation/dev-guide/api_reference_v3.md). You could find the correspondant path and request/responce parameters of certain API.

Also the official go client is [here](https://github.com/etcd-io/etcd/tree/master/clientv3), which could be a good reference.

---

# Test on multiple etcd locally

Very simple. Just replace etcd and etcdctl of different versions in system path. No need to build docker to separate environment.

Currently I have file to automatically switch etcd version, and run goreman. Wait to combine them together.

---

# Current workflow

Function code + test code

​					↓

Test by repeative ngx.say(inspect(res))

​	[inspect](https://github.com/kikito/inspect.lua) is a lua library to transforms any Lua value into a human-readable representation.

​					↓

Use luacheck and reindex to to the linting.

​	But it seems luacheck could not tell me I forgot to add space besides `=` ... Still not any clue on this

