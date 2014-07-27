use Rex -feature => ['0.51'];

# Load Keepalived Module
include "Keepalived";
include "HAProxy";

# configure authentication
user "root";
password "box";
pass_auth;

# create some groups
group lb => "192.168.9.100";

desc "Setup Keepalived";
task "setup",
  group => "lb",
  make {
  setup Keepalived;
  setup HAProxy;
  };

1;
