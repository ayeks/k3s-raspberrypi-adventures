# k3s-raspberrypi-adventures

HowTo run containers on a RaspberryPI ARM Cluster with Kubernetes (k3s). This repo is a collection of installation guides and HowTos. The goal is to show metrics in Grafana. Everything should be hosted in k3s.

## Known Pitfalls

* popular helm templates are often useless because they contain non-ARM docker images
* helm templates for old k8s releases are often useless because the API changed to much since then
* k3s ingress controller is traefik and it is not that easy to use because no documentation exists

## Installation

### Setup SD Cards with HypriotOS

Flashtool: https://github.com/hypriot/flash

```bash
./flash -u ./device-init_node01.yaml ./hypriotos-rpi-v1.11.3.img
```

Cloudinit: https://cloudinit.readthedocs.io/

### Setup k3s on master node

Install k3s:

```bash
curl -sfL https://get.k3s.io | sh -
```

Check Status:

```bash
sudo systemctl status k3s
```

Get join key for other nodes:

```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```

### Setup k3s on slave nodes

Install k3s and set it up. `K3S_URL` should be your master node. `K3S_TOKEN` should be your join key.

```bash
export K3S_URL="https://node01:6443"
export K3S_TOKEN="K1033794ef5348dacc8041234547ff57d2e2d34cb3ab1d99ac4904b95485::server:c7b43314a7c43821d0b6006671e22dc"
curl -sfL https://get.k3s.io | K3S_URL=${K3S_URL} K3S_TOKEN=${K3S_TOKEN} sh -
```

#### Node password rejected error

If the node was already added to the cluster before the following message will appear in the syslog at the node:

```text
Nov  2 13:25:46 node01 k3s[3427]: time="2019-11-02T13:25:46.686584162+01:00" level=error msg="Node password rejected, contents of '/var/lib/rancher/k3s/agent/node-password.txt' may not match server passwd entry"
```

You have to remove the password entry on the master for the node.

```bash
sudo nano /var/lib/rancher/k3s/server/cred/node-passwd
```

### Setup kubectl on your admin machine

To be able to use `kubectl` from your local host machine your can setup the config with [k3sup](https://github.com/alexellis/k3sup).

```bash
curl -sLS https://get.k3sup.dev | sh
sudo install k3sup /usr/local/bin/
```

Get the kubectl file of an master node where k3s is already installed. Change the `ip` to your master node.

```bash
k3sup install --skip-install --ip 192.168.178.76 --user pirate
```

Move the kubeconfig to your local config:

```bash
mv kubeconfig ~/.kube/config
```

## Related Repositories

Kubernetes is moving fast and introduces breaking changes along that way. Check always the k8s/k3s version of tutorials with your installed version. Otherwise you end up migrating yaml files to a new kube version all day long.

* [thibmaek/awesome-raspberry-pi](https://github.com/thibmaek/awesome-raspberry-pi)
  * A curated list of awesome Raspberry Pi tools, projects, images and resources
  * latest commit: Jan 2020
* [aapit/ansible-k3s-rpi](https://github.com/aapit/ansible-k3s-rpi)
  * Ansible playbook to provision a cluster of Raspberry Pi's with k3s, streamlined Kubernetes by @rancher
  * latest commit: Jul 2019
* [rcarmo/raspi-cluster](https://github.com/rcarmo/raspi-cluster)
  * Notes and scripts for setting up (yet another) Raspberry Pi computing cluster 
  * latest commit: Jul 2019
* [Sheldonwl/rpi-cluster-k3s](https://github.com/Sheldonwl/rpi-cluster-k3s)
  * This is a guide on how to build a six node Raspberry Pi cluster running K3s: Lightweigt Kubernetes
  * latest commit: Jun 2019
* [ifarfan/k3s-on-raspberrypi](https://github.com/ifarfan/k3s-on-raspberrypi)
  * Stand up a k3s cluster on a bunch of raspberry pis 
  * latest commit: Jul 2019 
* [rgulden/k3s-raspberry-pi](https://github.com/rgulden/k3s-raspberry-pi)
  * Instructions on how to set up a Raspberry Pi cluster using k3s by Rancher. 
  * latest commit: Nov 2019
* [edenreich/k3s-blue-green-deployment](https://github.com/edenreich/k3s-blue-green-deployment)
  * After setting up k3s on raspberry, this simple guide will walk you through making a blue-green deployment on your cluster 
  * latest commit: Jan 2020
* [se1exin/k8s-rpi](https://github.com/se1exin/k8s-rpi)
  * Kubernetes on Raspberry Pi Cluster - K3s w/ Traefik and LetsEncrypt - ansible playbooks for automated setup 
  * latest commit: Jan 2020
* [boyroywax/k3s-armhf-start](https://github.com/boyroywax/k3s-armhf-start)
  * Provisioning and Accessing k3s on raspberry pi 3+ home cluster 
  * latest commit: Aug 2019
* [mak3r/k3s-arm-techcon-2019](https://github.com/mak3r/k3s-arm-techcon-2019)
  * k3s demo for Arm TechCon 2019. Demo of morphing masters using k3s on raspberry pi devices 
  * latest commit: Nov 2019


