# Web Architecture Example - Ansible


The architecture for the example web application will be:

                       --------------------------
                      |  varnish.test (Varnish)  |
                      |  192.168.2.2             |
                       --------------------------
                          /                   \
         ----------------------          ----------------------
        |  www1.test (Apache)  |        |  www2.test (Apache)  |
        |  192.168.2.3         |        |  192.168.2.4         |
         ----------------------          ----------------------
                          \                   /
                      ------------------------------
                     |  memcached.test (Memcached)  |
                     |  192.168.2.7                 |
                      ------------------------------
                          /                   \
     -----------------------------       ----------------------------
    |  db1.test (MySQL - Master)  |     |  db2.test (MySQL - Slave)  |
    |  192.168.2.5                |     |  192.168.2.6               |
     -----------------------------       ----------------------------

*IP addresses and hostnames in this diagram are modeled after local VirtualBox/Vagrant-based VMs.*

This architecture offers multiple levels of caching and high availability/redundancy on almost all levels, though to keep it simple, there are single points of failure. All persistent data stored in the database is stored in a slave server, and one of the slowest and most constrained parts of the stack (the web servers, in this case running a PHP application through Apache) is easy to scale horizontally, behind Varnish, which is acting as a caching (reverse proxy) layer and load balancer.

For the purpose of demonstration, Varnish's caching is completely disabled, so you can refresh and see both Apache servers (with caching enabled, Varnish would cache the first response then keep serving it without hitting the rest of the stack). You can see the caching and load balancing configuration in `playbooks/varnish/templates/default.vcl`).

## Prerequisites

Before you can run any of these playbooks, you will need to [install Ansible](http://docs.ansible.com/intro_installation.html), and run the following command to download dependencies (from within the same directory as this README file):

    $ ansible-galaxy install -r requirements.yml



## Build and configure the servers (Local)

To build the VMs and configure them using Ansible, follow these steps (from within this directory):

  1. Run `vagrant up`.
