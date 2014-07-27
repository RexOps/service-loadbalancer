use Rex -feature => ['0.51'];
use Rex::CMDB;

# Load Keepalived Module
include "Keepalived";
include "HAProxy";

# configure the CMDB
# we're using the default YAML CMDB
# but we're defining some special lookup rules
set cmdb => {
  type => "YAML",
  path => [
    "cmdb/{operatingsystem}/{hostname}.yml",
    "cmdb/{operatingsystem}/default.yml",
    "cmdb/{environment}/{hostname}.yml",
    "cmdb/{environment}/default.yml",
    "cmdb/{hostname}.yml",
    "cmdb/default.yml",
  ],
};

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
