# Ansible Playbook for Cosmos Nodes

The purpose of this project is to help you setup nodes from Cosmos echosystem very quickly using Ansible.

You could take any server and this Ansible will do all work for you. It will install all dependencies, build the project and init it.

# How to use this project

## Step 1 - Install Ansible

You could find how to install ansible [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

## Step 2 - Install additional Ansible Community Packages

`ansible-galaxy collection install community.general`

## Step 3 - Configure inventory.txt file

In this file you should define hostname, ip address of the node, username and ssh port. Here is example:

`umee_node ansible_host=12.5.168.15 ansible_user=root ansible_port=22`

`umee_node` -> it is hostname

`12.5.168.15` -> it is ip address of you node

`root` -> it is username for ssh login

`22` -> it is ssh port to connect to

## Step 4 - Configure properties file for particular project and network

In this example we will take a look at Umee project and Umeemania testnet. The file is `umee_umeemanina_testnet.json`.

Here is content of this file:

```
{
  "moniker": "your_moniker",
  "project_name": "umee",
  "project_command": "umeed",
  "project_path": ".umee",
  "chain_id": "umeemania-1",
  "repo": "https://github.com/umee-network/umee.git",
  "version": "facu/fix-testnet-halt",
  "persistent_peers": "67cd50cac0eff503a7c9efd537d369cc7a3ebe96@34.83.162.159:26656,08f30f7119ae1317082d50fac6a08e6f2400126f@34.83.251.68:26656,08840edc6775bd7a298269f3343c554e5af0359f@35.202.178.94:26656,2e5320f284b65e388d968626825f3ffbf3f3f985@34.97.181.238:26656",
  "link_to_genesis_file": "https://raw.githubusercontent.com/umee-network/testnets/main/networks/umeemania-1/genesis.json",
  "ssh_port": 22,
  "node_port": 26656,
  "go_version": 1.17
}

```

Lets go through the properties that you have to define:

- moniker - it is the moniker of your node, for example your name or name of your company
- project_name - the name of the folder where we are going to download Umee source code
- project_command - the name of the program that is used to call Umee
- project_path - the default location where Umee stores configuration and data
- chain_id - chain id of the network
- repo - Umee project repository
- version - repository tag or branch to checkout
- persistent_peers - peers to connect
- link_to_genesis_file - ling to the genesis file
- ssh_port - ssh port to connect to
- node_port - node port to communicate with other nodes
- go_version - version of go to build the project

This playbook will open ports only to ssh_port and node_port. All other ports will be closed.

## Step 5 - Init the node

In this step all installations and configurations will be done. The node will not start.

To run playbook you should apply ansible playbook. Here is example for Ummemania testnet:

`ansible-playbook pb_init_node.yml -i inventory.txt --extra-vars "host=umee_node" --ask-become-pass --extra-vars "@umee_umeemania_testnet.json"`

`--ask-become-pass` if you login as a user other than root, you will need to provide your sudo password

## Step 6 - Run the node

`ansible-playbook pb_start_service.yml -i inventory.txt --ask-become-pass --extra-vars "host=umee_node service_name=umeed.service"`

## Step 7 - Check logs of you node

`journalctl -u umeed.service -f -o cat`

## Step 8 - !!! backup all your keys !!!

