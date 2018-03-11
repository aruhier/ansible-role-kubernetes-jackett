Ansible Role: Jackett for Kubernetes
====================================

Ansible role to install [jackett](https://github.com/Jackett/Jackett) on
Kubernetes.

Role Variables
--------------

```yaml
# Image used
kubernetes_jackett_image: "linuxserver/jackett:latest"

# Namespace
kubernetes_jackett_namespace: "default"
# App name (used as selector)
kubernetes_jackett_app: "jackett"
# Deployment name
kubernetes_jackett_deployment: "jackett-deployment"
# Service name
kubernetes_jackett_service: "jackett"

# Number of replicas
kubernetes_jackett_replicas: 1
kubernetes_jackett_revision_history: 1

# Node selector
kubernetes_jackett_node_selector: {}

# Add custom labels in the deployment metadata section
kubernetes_jackett_deployment_labels: {}
# Add custom annotations in the deployment metadata section
kubernetes_jackett_deployment_annotations: {}

kubernetes_jackett_resources:
  limits:
    memory: "1Gi"
  requests:
    memory: "256Mi"

# Setup ingress for jackett
kubernetes_jackett_setup_ingress: false
kubernetes_jackett_ingress:
  name: "jackett-ingress"
  host: "jackett.example.com"
  annotations:
  tls:

### Volumes ###

# For each volume, `definition` and `subPath` can be used. Their content is
# exactly what would be in a Kkubernetes pod manifest.

# Useful when blackhole is used. Mounted in /downlaods/ (see # examples for
more details)
kubernetes_jackett_blackhole_volume: {}

# jackett config volume. Contains the database, arts cache and config.
kubernetes_jackett_config_volume:
  definition:
```

Dependencies
------------

Kubectl needs to be installed on the host targeted by the role.


Example Playbook
----------------

```yaml

- hosts: kube-master
  run_once: true
  vars:
    # Directory for the torrents blackhole
    kubernetes_jackett_baclhole_volume:
      # This volume will be mounted as /downloads/
      definition:
        glusterfs:
          endpoints: gluster-example-cluster
          path: volume-blackhole
          readOnly: false
      subPath: "example/providers"

    kubernetes_jackett_config_volume:
      definition:
        glusterfs:
          endpoints: gluster-example-cluster
          path: volume-jackett
          readOnly: false
        subPath: "config"

    kubernetes_jackett_setup_ingress: true
    kubernetes_jackett_ingress:
      name: "jackett-ingress"
      host: "jackett.example.com"
      annotations:
        kubernetes.io/tls-acme: "true"
      tls:
        - secretName: "jackett-ingress-tls"
          hosts:
            - "jackett.example.com"
  roles:
    - role: Anthony25.kubernetes-jackett
```

Use `run_once` to run the role on only one available master in the cluster.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
