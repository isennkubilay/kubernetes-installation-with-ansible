Ensure necessary ports are open between nodes. Common ports include:

- **Kubernetes API Server**: 6443
- **Etcd Server Client API**: 2379-2380
- **Kubelet API**: 10250
- **NodePort Services**: 30000-32767

1. `ansible-playbook -i inventory/hosts.yml playbooks/set_hostnames.yml`

2. `ansible-playbook -i inventory/hosts.yml playbooks/before_installation.yml`

3. `ansible-playbook -i inventory/hosts.yml playbooks/install_docker.yml`

4. `ansible-playbook -i inventory/hosts.yml playbooks/install_containerd.yml`

5. `ansible-playbook -i inventory/hosts.yml setup_kubernetes.yml`

6. `ansible-playbook -i inventory/hosts.yml playbooks/install_keepalived_haproxy.yml`

7. `ansible-playbook -i inventory/hosts.yml playbooks/initialize_cluster.yml`

8. `ansible-playbook -i inventory/hosts.yml playbooks/install_calico.yml`

9. `ansible-playbook -i inventory/hosts.yml setup_workers.yml`


                              +--------------------+
                              |                    |
                              |    kubectl CLI     |
                              |                    |
                              +---------+----------+
                                        |
                                        |
                                        |
                                        v
                      +-----------------+-----------------+
                      |                                   |
                      |          Master Node              |
                      |                                   |
                      +--------+---------+--------+-------+
                               |         |        |
                               |         |        |
                               |         |        |
                               v         v        v
                      +--------+---+ +---+--------+--------+
                      |            | |          |          |
                      | API Server | | Scheduler|  etcd    |
                      |            | |          |          |
                      +-----+------+ +---+------+---+------+
                            |            |          |
                            v            |          |
                  +---------+------+     |          |
                  |                |     |          |
                  | Controller     |     |          |
                  | Manager        |     |          |
                  +----------------+     |          |
                                         |          |
                                         v          v
                                        +---+-------+---+
                                        |               |
                                        |     etcd      |
                                        |               |
                                        +---+-------+---+
                                            |       |
                                            v       v
                                    +-------+-------+-------+
                                    |                       |
                                    |      Worker Nodes     |
                                    |                       |
                                    +---------+-------------+
                                              |
                                              v
                              +---------------+---------------+
                              |                               |
                              |                               |
                              v                               v
             +----------------+-----------+   +--------------+-----------+
             | Kubelet                    |   | Kubelet                  |
             | Container Runtime          |   | Container Runtime        |
             +----------------+-----------+   +--------------+-----------+
                              |                               |
                              v                               v
             +----------------+-----------+   +--------------+-----------+
             |   Pods                      |   |   Pods                    |
             |                             |   |                           |
             +-----------------------------+   +---------------------------+
