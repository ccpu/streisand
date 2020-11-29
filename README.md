# For my own use.

# Required:

- both builder machine and server must have ubuntu 16

# Insturacion:

## Docker:

    Dockerfile File:
    	FROM ubuntu:16.04
    	RUN apt update && apt install  openssh-server curl -y

    docker run --rm -v LOCAL_SHARED_PATH:/data -it imageName /bin/bash

## VPN Builder machine

Enable opnevpn and make sure docker using vpn (skip if trust network):

    curl ifconfig.me.

## For existing server setup(advance):

use builder machine to generate ssh private key and add it to target server:

## Builder machine:

    ls ~/.ssh
    ssh-keygen
    cp -r ~/.ssh data

## Target/Remote machine

copy id_rsa.pub content of data/.ssh (important: remove next lines):

    sudo nano ~/.ssh/authorized_keys
    sudo systemctl restart sshd

test if working in builder machine:

    ssh user@IP_ADDRESSorHOSTNAME

### v2ray Installation

    sudo add-apt-repository ppa:longsleep/golang-backports
    sudo apt-get update && sudo apt-get upgrade -y
    sudo apt-get install golang-go -y && cd /
    sudo git clone https://github.com/shadowsocks/v2ray-plugin && cd v2ray-plugin && sudo go build

## Builder machine:

    git clone https://github.com/ccpu/streisand.git && cd streisand
    ./util/venv-dependencies.sh ./venv
    source ./venv/bin/activate
    ./streisand

> If complained about user, use `ANSIBLE_SSH_USER=ubuntu ./streisand` command

download generated-docs

    cp -r streisand/generated-docs/ data

## Test and cleanup Target/Remote machine

remove v2ray-plugin plugin folder

    cd / && sudo rm -r v2ray-plugin

After reboot should not be able to login with password but only ssh key.

    sudo reboot

Verify that the shadowsocks server is running and clean up

    systemctl status shadowsocks-libev.service

# Troubleshooting

### V2RAY-PLUGIN

If installation complain about not able to copy v2ray copy v2ray-plugin manually as follow:

comment out line 31/32 of playbooks/roles/shadowsocks/tasks/v2ray.yml
and run setup again and copy v2ray-plugin manually (cp -rf /v2ray-plugin/v2ray-plugin /etc/shadowsocks-libev)

### OPENVPN

IF need update openvpn signed key and api key form:
get api key and sign key from:
https://community.openvpn.net/openvpn/wiki/OpenvpnSoftwareRepos

Files:
playbooks/roles/openvpn/files/openvpn_signing.key
playbooks/roles/openvpn/tasks/install.yml
playbooks/roles/test-client/files/openvpn_signing.key

# refs:

https://github.com/StreisandEffect/streisand/blob/master/Installation.md

https://support.us.ovhcloud.com/hc/en-us/articles/115001588250-How-to-Use-SSH-Keys

https://www.linuxbabe.com/ubuntu/shadowsocks-libev-proxy-server-ubuntu

# Issue

## FAILED - RETRYING: Refresh the Streisand GPG keyring with keyserver information (10 retries left).

https://github.com/StreisandEffect/streisand/issues/1822
https://community.openvpn.net/openvpn/wiki/OpenvpnSoftwareRepos
https://github.com/StreisandEffect/streisand/pull/1796

## v2ray-plugin

https://github.com/StreisandEffect/streisand/issues/1819

sudo nano /etc/shadowsocks-libev/config.json
