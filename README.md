# Turing Pi Cluster - 7-node K3s Raspberry Pi Cluster

<p align="center"><a href="https://www.youtube.com/watch?v=kgVz4-SEhbE"><img src="images/turing-pi-cluster-hero.jpg?raw=true" width="500" height="auto" alt="Turing Pi - Raspberry Pi Compute Module Cluster" /></a></p>

This repository is a companion to a YouTube series by Jeff Geerling in 2020:

  - **Episode 1**: [Introduction to Clustering](https://www.youtube.com/watch?v=kgVz4-SEhbE) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-1-introduction-clusters))
  - **Episode 2**: [Setting up the Cluster](https://www.youtube.com/watch?v=xNndbfxMCLo) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-2-setting-cluster))
  - **Episode 3**: [Installing K3s on the Turing Pi](https://www.youtube.com/watch?v=N4bfNefjBSw) ([blog post](https://www.jeffgeerling.com/blog/2020/pi-cluster-episode-3-installing-k3s-kubernetes-on-turing-pi))
  - **Episode 4**: Coming soon!

You might also be interested in another Raspberry-Pi cluster I've maintained for years, the [Raspberry Pi Dramble](https://www.pidramble.com), which is a Kubernetes Pi cluster in my basement that hosts the Drupal website [www.pidramble.com](https://www.pidramble.com).

## Usage

First, you need to make sure you have K3s running on your Pi cluster. Instructions for doing so are in Episodes 2 and 3 (linked above).

Also make sure you have `extra_server_args: "--node-taint k3s-controlplane=true:NoExecute"` in your K3s `group_vars/all.yml`, so pods are not scheduled on the master node.

Then, you can deploy _all_ the applications configured in this repository with the `main.yml` playbook:

  1. Make sure you have [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) installed.
  2. Make sure you have the Kubernetes config file for the Turing Pi cluster available locally in the path `~/.kube/config-turing-pi`.
  3. Copy the `example.hosts.ini` inventory file to `hosts.ini`. Make sure it has the `master` and `node`s configured correctly.
  4. Run the playbook:

     ```
     ansible-playbook main.yml
     ```

Once that's done, there will be a huge variety of stuff running on your cluster, including:

  - Prometheus, AlertManager, and Grafana - for deep insights and cluster monitoring.
  - Drupal - a popular open-source CMS deployed with the [Drupal Operator](https://github.com/geerlingguy/drupal-operator).

## Resetting the cluster

Especially when you're starting out with Kubernetes, but even if you're an expert, you'll likely want to blow away all the changes you've made in a cluster and start fresh. If you made a mistake, or something broke terribly, that problem goes away with the cluster. Or, if you want to make sure you've automated the entire cluster build properly, it's best practice to blow up the cluster and rebuild it from time to time.

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

## Author

The repository was created by [Jeff Geerling](https://www.jeffgeerling.com), who writes [Ansible for DevOps](https://www.ansiblefordevops.com) and [Ansible for Kubernetes](https://www.ansibleforkubernetes.com).
