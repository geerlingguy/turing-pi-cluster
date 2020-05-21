# Turing Pi Cluster - 7-node K3s Raspberry Pi Cluster

This repository is a companion to a YouTube series by Jeff Geerling in 2020:

  - **Episode 1**: [Raspberry Pi Cluster Ep 1 - Introduction to Clustering](https://www.youtube.com/watch?v=kgVz4-SEhbE) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-1-introduction-clusters))
  - **Episode 2**: [Raspberry Pi Cluster Ep 2 - Setting up the Cluster](https://www.youtube.com/watch?v=xNndbfxMCLo) ([blog post](https://www.jeffgeerling.com/blog/2020/raspberry-pi-cluster-episode-2-setting-cluster))
  - **Episode 3**: [Installing K3s on the Turing Pi - Raspberry Pi Cluster Ep 3](https://www.youtube.com/watch?v=N4bfNefjBSw) ([blog post](https://www.jeffgeerling.com/blog/2020/pi-cluster-episode-3-installing-k3s-kubernetes-on-turing-pi))

## Usage

First, you need to make sure you have K3s running on your Pi cluster. Instructions for doing so are in Episodes 2 and 3 (linked above). Then, you can deploy _all_ the applications configured in this repository with the `main.yml` playbook:

  1. Make sure you have [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) installed.
  2. Make sure you have the Kubernetes config file for the Turing Pi cluster available locally in the path `~/.kube/config-turing-pi`.
  3. Copy the `example.hosts.ini` inventory file to `hosts.ini`. Make sure it has the `master` and `node`s configured correctly.
  4. Run the playbook:

     ```
     ansible-playbook main.yml
     ```

TODO: Profit!

## Author

The repository was created by [Jeff Geerling](https://www.jeffgeerling.com), who writes [Ansible for DevOps](https://www.ansiblefordevops.com) and [Ansible for Kubernetes](https://www.ansibleforkubernetes.com).