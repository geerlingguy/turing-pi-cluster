# Turing Pi Cluster - 7-node K3s Raspberry Pi Cluster

[![CI](https://github.com/geerlingguy/turing-pi-cluster/workflows/CI/badge.svg?branch=master&event=push)](https://github.com/geerlingguy/turing-pi-cluster/actions?query=workflow%3ACI)

<p align="center"><a href="https://www.youtube.com/watch?v=kgVz4-SEhbE"><img src="images/turing-pi-cluster-hero.jpg?raw=true" width="500" height="auto" alt="Turing Pi - Raspberry Pi Compute Module Cluster" /></a></p>

This repository is a companion to a YouTube series by Jeff Geerling in 2020:

  - **Episode 1**: [Introduction to Clustering](https://www.youtube.com/watch?v=kgVz4-SEhbE) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-1-introduction-clusters))
  - **Episode 2**: [Setting up the Cluster](https://www.youtube.com/watch?v=xNndbfxMCLo) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-2-setting-cluster))
  - **Episode 3**: [Installing K3s on the Turing Pi](https://www.youtube.com/watch?v=N4bfNefjBSw) ([blog post](https://www.jeffgeerling.com/blog/2020/pi-cluster-episode-3-installing-k3s-kubernetes-on-turing-pi))
  - **Episode 4**: [Minecraft, Pi-hole, Grafana + MORE!](https://www.youtube.com/watch?v=IafVCHkJbtI) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-4-minecraft-pi-hole-grafana-and-more))
  - **Episode 5**: [Benchmarking the Turing Pi](https://www.youtube.com/watch?v=IoMxpndlDWI) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-5-benchmarking-turing-pi))
  - **Episode 6**: [Turing Pi Review](https://www.youtube.com/watch?v=aApByQWqnV0) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-6-turing-pi-review))

You might also be interested in another Raspberry-Pi cluster I've maintained for years, the [Raspberry Pi Dramble](https://www.pidramble.com), which is a Kubernetes Pi cluster in my basement that hosts [www.pidramble.com](https://www.pidramble.com).

## Compatibility

This cluster configuration has been tested with the following Raspberry Pi and OS combinations:

  - Raspberry Pi 4 model B and HypriotOS
  - Raspberry Pi 4 model B and Raspberry Pi OS (32-bit)
  - Raspberry Pi 4 model B and Raspberry Pi OS (64-bit)
  - Raspberry Pi Compute Module 3+ and HypriotOS using a [Turing Pi](https://turingpi.com/)

Other models of Raspberry Pi and Compute Modules may or may not work, but the main thing you need is a cluster with at least 7 GB of RAM and at least 12 available CPU cores (every current Pi has 4 CPU cores), otherwise not all of the software will be able to run well.

This configuration will _definitely not_ run on the Pi Zero, or on Pis older than the Raspberry Pi 2 model B.

## Usage

First, you need to make sure you have K3s running on your Pi cluster. Instructions for doing so are in Episodes 2 and 3 (linked above).

When you run the K3s Ansible playbook, make sure you have `extra_server_args: "--node-taint k3s-controlplane=true:NoExecute"` in your K3s `group_vars/all.yml`, so pods are not scheduled on the master node, and that all your nodes have unique hostnames (e.g. on Pi OS, run `sudo hostnamectl set-hostname worker-01` to set a Pi to `worker-01`).

Then, you can deploy _all_ the applications configured in this repository with the `main.yml` playbook:

  1. Make sure you have [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) installed.
  2. Install Ansible requirements:

     ```
     ansible-galaxy role install -r requirements.yml
     ansible-galaxy collection install -r requirements.yml
     ```

     These commands can be consolidated into one `ansible-galaxy install` command once Ansible 2.10 is released.

  3. Copy the `example.hosts.ini` inventory file to `hosts.ini`. Make sure it has the `master` and `node`s configured correctly.
  4. Edit the `ingress_server_ip` and `load_balancer_server_ip` in `group_vars/all.yml` and set them each to an IP address of one of the nodes. (Change any other variables in that file as necessary.)
  5. Run the playbook:

     ```
     ansible-playbook main.yml
     ```

Once that's done, there will be variety of applications running on your cluster, for example:

| Software | Address | Notes |
| -------- | ------- | ------- |
| Prometheus | http://prometheus.10.0.100.74.nip.io/ | N/A |
| AlertManager | http://alertmanager.10.0.100.74.nip.io/ | N/A |
| Grafana | http://grafana.10.0.100.74.nip.io/ | Default login is `admin`/`admin` |
| Drupal | http://drupal.10.0.100.74.nip.io/ | N/A |
| Wordpress | http://wordpress.10.0.100.74.nip.io/ | N/A |
| Minecraft | (`kubectl get service -n minecraft`) | See EULA in [Minecraft chart repo](https://github.com/helm/charts/tree/master/stable/minecraft) |
| Pi-hole | http://pi.hole/ | See [pihole role README](roles/pihole/README.md) |

The exact URLs will vary in your cluster; refer to the output of the Ansible playbook, which lists each service's exact URL.

## Caveats

They are a'plenty.

First of all, the configurations in this repository were built for local demonstration purposes. There are some things that are insecure (like storing some database passwords in plain text), and other things that are just plain crazy (like trying to run all the above things on one tiny Pi-based cluster!).

There are a few architectural decisions that were made that are great for 'day one' setup, but if you tried to flex K3s' muscle and drop/replace nodes while the cluster is running, you'd likely start running into some, shall we say, 'fun' problems.

For example, the MariaDB PVCs are tied to the local node on which they were first deployed, and if you do something that results in the MariaDB Deployment to change nodes for the deployed Pod... you may run into warnings like `FailedScheduling: 3 node(s) had volume node affinity conflict.`

Therefore, if you want to use this project as a base, and are planning on doing anything more than a local demo cluster, you are responsible for making changes to support a more production-ready setup, with better security and better configuration of persistent volumes and multi-pod scalability.

To do these things _correctly_ with Kubernetes takes a lot of work. It's usually _very_ easy—maybe deceptively easy—to get something working. It's harder to get it working reliably in an automated fashion when rebuilding the cluster from scratch (that's about the level where this repository is). And harder still is getting it working reliably with easy maintenance, fault-tolerance, and scalability.

Kubernetes is no substitute for a thorough knowledge of system architecture and engineering!

## Resetting the cluster

You'll likely want to blow away all the changes you've made in a cluster and start fresh every now and then. If you made a mistake, or something broke terribly, that problem goes away. Or, if you want to make sure you've automated the entire cluster build properly, it's best practice to rebuild a cluster frequently.

Regardless of the reason, here's how to quickly wipe the cluster clean (without re-flashing all the Raspberry Pis from scratch):

  1. In the `k3s-ansible` repository directory (which you used to set up the cluster), run:

     ```
     ansible-playbook -i inventory/hosts.ini reset.yml
     ```

     This command will likely have a few failures relating to files that can't be cleaned up until after a reboot.

  2. Reboot the Raspberry Pis (in the same directory):

     ```
     ansible -i inventory/hosts.ini all -m reboot -b
     ```

  3. Run the reset playbook a second time, to clean up the stragglers:

     ```
     ansible-playbook -i inventory/hosts.ini reset.yml
     ```

  4. Re-install K3s on the cluster:

     ```
     ansible-playbook -i inventory/hosts.ini site.yml
     ```

Now you can go back to the steps above under 'Usage' to set up applications inside the cluster!

> Important note: Any files that were downloaded for this repository, like the monitoring repository, still exist in the `pirate` (HypriotOS) or `pi` (Raspberry Pi OS) user's home directory. For a more complete reset, also delete all those files and directories. Or to go thermonuclear, re-flash all the Pi's eMMC or microSD cards.

## Author

The repository was created in 2020 by [Jeff Geerling](https://www.jeffgeerling.com), who writes [Ansible for DevOps](https://www.ansiblefordevops.com) and [Ansible for Kubernetes](https://www.ansibleforkubernetes.com).
