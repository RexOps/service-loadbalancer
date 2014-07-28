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
