# OpenSCAP agent

The OpenSCAP agent allows you to obtain information about your OpenSCAP
installation as well as running both targeted and full scans against a system.

The system defaults to working with the [SSG](https://github.com/OpenSCAP/scap-security-guide "SCAP Security Guide (SSG)") materials.

Currently, this Agent has only been tested on:

* RHEL 7
* CentOS 7

To use this agent you need at least:

* MCollective 2.3.2
* The Nokogiri Rubygem
    * If using PE, install the pe-cloud-provisioner-libs
* The scap-security-guide package
* The openscap-scanner package

Please report any errors or make feature requests in the
[MCollective OpenSCAP Project](https://github.com/onyxpoint/mcollective-openscap-agent "MCollective OpenSCAP Project")

## Installation

Follow the [basic plugin install guide][install guide], taking all
the code from lib and adding it to your MCollective $libdir

[install guide]: http://projects.puppetlabs.com/projects/mcollective-plugins/wiki/InstalingPlugins


## Configuring the agent

The agent should work without any additional configuration.

## Application usage

The `mco oscap` application has several subcommands to interact with an
OpenSCAP installation.

### mco oscap profiles

Obtains a list of available profiles on the client systems.


```shell
$ mco oscap profiles

 * [ ============================================================> ] 1 / 1

master
  OpenSCAP Profiles: ["rht-ccp"]
```

### mco oscap oval_checks

Returns a list of OVAL checks, with human readable names.

It is highly recommended that you make this a targeted check.

```shell
mco oscap oval_checks

 * [ ============================================================> ] 1 / 1

master
   OVAL Checks: ["partition_for_tmp => oval:ssg:def:272",
                 "partition_for_var => oval:ssg:def:151",
                 "partition_for_var_log => oval:ssg:def:334",
                 "partition_for_var_log_audit => oval:ssg:def:285",
                 "partition_for_home => oval:ssg:def:312",
                 "ensure_redhat_gpgkey_installed => oval:ssg:def:314",
                 "ensure_gpgcheck_globally_activated => oval:ssg:def:140",
                 "ensure_gpgcheck_never_disabled => oval:ssg:def:142",
                 "package_aide_installed => oval:ssg:def:279",
                 "disable_prelink => oval:ssg:def:579",
                 "rpm_verify_permissions => oval:ssg:def:580",
                 "rpm_verify_hashes => oval:ssg:def:581",
                 ...
```

### mco oscap scan

Runs a scan against the systems and returns the failure/pass rate with a report
of failing nodes.

```shell
mco oscap scan -p rht-ccp -i ALL 

 * [ ============================================================> ] 1 / 1

master
   Scan Results: 
          Score: 60.239197
```

Full output can also be obtained with the *-f* flag

This will return a great deal of data, so a targeted check is advised if you
are not going to pass the output to an automated processor in JSON form.

```shell
mco oscap scan -p rht-ccp -i ALL -f

 * [ ============================================================> ] 1 / 1

master
   Scan Results: {"partition_for_tmp"=>{:severity=>"low", :result=>"fail"},
                  "partition_for_var"=>{:severity=>"low", :result=>"fail"},
                  "partition_for_var_log"=>{:severity=>"low", :result=>"fail"},
                  "partition_for_var_log_audit"=>{:severity=>"low", :result=>"fail"},
                  "ensure_redhat_gpgkey_installed"=>{:severity=>"high", :result=>"fail"},
                  "ensure_gpgcheck_globally_activated"=>{:severity=>"high", :result=>"pass"},
                  "ensure_gpgcheck_never_disabled"=>{:severity=>"high", :result=>"pass"},
                  "security_patches_up_to_date"=>{:severity=>"high", :result=>"notchecked"},
                  "package_aide_installed"=>{:severity=>"medium", :result=>"fail"},
                  "userowner_shadow_file"=>{:severity=>"medium", :result=>"pass"},
                  "groupowner_shadow_file"=>{:severity=>"medium", :result=>"pass"},
                  "file_permissions_etc_shadow"=>{:severity=>"medium", :result=>"pass"},
                  "file_owner_etc_group"=>{:severity=>"medium", :result=>"pass"},
                  "file_groupowner_etc_group"=>{:severity=>"medium", :result=>"pass"},
                  "file_permissions_etc_group"=>{:severity=>"medium", :result=>"pass"},
                  "file_owner_etc_gshadow"=>{:severity=>"medium", :result=>"pass"},
                  "file_groupowner_etc_gshadow"=>{:severity=>"medium", :result=>"pass"},
                  "file_permissions_etc_gshadow"=>{:severity=>"medium", :result=>"pass"},
                  "file_owner_etc_passwd"=>{:severity=>"medium", :result=>"pass"},
                  ...
          Score: 60.239197
```

Can also be used in targeted mode to evaluate a single OVAL scan

```shell
mco oscap scan -p rht-ccp -i file_user_owner_grub2_cfg

master
   Scan Results: Pass
          Score: 0
```

