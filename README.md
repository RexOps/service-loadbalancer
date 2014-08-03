# Howto setup a Loadbalancer with Keepalived and HAProxy with Rex

This is an example project that shows how to setup Keepalived and HAProxy with Rex.

## Used Tools

### Rex

Rex (http://www.rexify.org/) is a deployment and configuration management tool. Sometimes also called a server orchestration tool. Rex doesn't need an agent on your servers, it just needs a ssh account. It is possible to connect with the root user or any other user that can utilize sudo to gain administrative privileges.

Rex is controlled by a file called *Rexfile*. A *Rexfile* is similar to a *Makefile* but it is written in perl with a customized DSL to ease the writing of tasks.

An example *Rexfile* that just connects to a server and print the uptime looks like this:
```perl
user "root";
password "box";

task "uptime", "server1", make {
  my $output = run "uptime";
  say $output;
};
```

### Keepalive

Keepalived (http://www.keepalived.org/) routing software using LVS for layer 4 loadbalancing. In this example we're going to use its VRRP feature to silently fail over ip addresses between hosts.

From its website:

Keepalived is a routing software written in C. The main goal of this project is to provide simple and robust facilities for loadbalancing and high-availability to Linux system and Linux based infrastructures. Loadbalancing framework relies on well-known and widely used Linux Virtual Server (IPVS) kernel module providing Layer4 loadbalancing. Keepalived implements a set of checkers to dynamically and adaptively maintain and manage loadbalanced server pool according their health. On the other hand high-availability is achieved by VRRP protocol. VRRP is a fundamental brick for router failover. In addition, Keepalived implements a set of hooks to the VRRP finite state machine providing low-level and high-speed protocol interactions. Keepalived frameworks can be used independently or all together to provide resilient infrastructures.

### HAProxy

HAProxy (http://www.haproxy.org/) is a TCP/HTTP Loadbalancer.

From its website:

HAProxy is a free, very fast and reliable solution offering high availability, load balancing, and proxying for TCP and HTTP-based applications. It is particularly suited for very high traffic web sites and powers quite a number of the world's most visited ones.


## Project setup

To setup the project you can use the *rexify* command:

```bash
rexify --init=https://github.com/RexOps/service-loadbalancer.git
```

This command will clone the repository onto your harddrive and download also the needed dependencies defined in the *meta.yml* file.

## Project structure

### The Rexfile

The *Rexfile* is the main entry point for Rex. This file is pretty small, because we're using the CMDB feature to provide all the necesarry information for the used Keepalived and HAProxy modules.

The server groups are managed in the *server.ini* file.

```perl
use Rex -feature => ['0.51'];

# Load Keepalived Module
include "Keepalived";
include "HAProxy";

desc "Setup Keepalived";
task "setup",
  group => "lb",
  make {
  setup Keepalived;
  setup HAProxy;
  };

1;
```

In line **1** we just acivate all the new features of Rex version 0.51. If you're using an IDE like Eclipse it is also necesarry to do a *use Rex ...* at the begining of the *Rexfile* so the syntax validation will work.

In line **4** and **5** we load the Keepalived and HAProxy modules. It is also possible to use the perl internal *use* for it, but with the *include* function Rex will not disaply the tasks from these modules when listing the tasks with ```rex -T```.


In line **7** we set a description for the task in line **8**.

In line **8** we create a task that gets executed on the server group **lb**. This task just calls the *setup* task of *Keepalived* and *HAProxy* module.

Both modules will get their configuration out of the CMDB.

### The CMDB

In this project we're using the default CMDB provided by Rex. This CMDB is uses YAML files. It is also easy to write custom CMDB providers, but this is a chapter of its own.

The CMDB works the way, that if you request an item from the CMDB it will lookup the item in a defined direction (the lookup path). This lookup path can be customized. By default it will follow this:

* cmdb/{operatingsystem}/{hostname}.yml
* cmdb/{operatingsystem}/default.yml
* cmdb/{environment}/{hostname}.yml
* cmdb/{environment}/default.yml
* cmdb/{hostname}.yml
* cmdb/default.yml

So in this project we use the file *cmdb/default.yml* for the HAProxy settings and some basic Keepalived settings which are the same for both nodes. And *cmdb/{hostname}.yml* for the node specific settings.

## Testing the example

If you want to run this example you need the following things:

* 2 machines to install HAProxy and Keepalived
* 1 or more machines for your real servers (setup is not part of this example)

If you want to use the special HAProxy feature to send the client ip to the real servers, you also need to configure the real servers to use the Keepalived/HAProxy machines as a default gateway.




