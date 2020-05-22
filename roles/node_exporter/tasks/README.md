# Node Exporter - Ansible Role

This role installs Prometheus' [Node exporter](https://github.com/prometheus/node_exporter) on Linux hosts, and configures a systemd unit file so the service can run and be controlled by systemd.

**Note**: If you're running in a Kubernetes cluster, you could run Node exporter as a DaemonSet in the cluster, instead of installing it on individual nodes.

## Variables

See `defaults/main.yml`:

    node_exporter_version: '0.18.1'

The version of Node Exporter to install. If you change the version, the `node_exporter` binary will be replaced with the newer version and the service will be restarted.

    node_exporter_arch: '{{ ansible_architecture }}'
    node_exporter_download_url: https://github.com/prometheus/node_exporter/releases/download/v{{ node_exporter_version }}/node_exporter-{{ node_exporter_version }}.linux-{{ node_exporter_arch }}.tar.gz

Download options for the `node_exporter` binary.

    node_exporter_bin_path: /usr/local/bin/node_exporter

The location where the `node_exporter` binary will be installed.

    node_exporter_options: ''

Any extra configuration to pass to the `node_exporter` command. For example, to disable cpu statistics, set this to `'--no-collector.cpu'`.

    node_exporter_state: started
    node_exporter_enabled: true

Controls for the `node_export` service.

## Author

This role was created in 2020 by [Jeff Geerling](https://www.jeffgeerling.com).
