# Oasis Node Ansible

A complete Ansible setup to deploy and manage multiple Oasis nodes (validator, runtime, etc.) on a single server for both mainnet and testnet. Designed to be simple, efficient, and easy to use, even for beginners.

## Features

- **Multi-Node, Single-Server:** Run a validator and multiple runtime nodes on the same server.
- **Network Separation:** Mainnet and testnet nodes can coexist on the same host without conflicts.
- **Automated & Simple:** The role automatically handles unique users, ports, and service names for each node instance.
- **Always Up-to-Date:** Fetches the latest recommended version, seeds, and checkpoint data from official sources.
- **Helpful Aliases:** Deploys a `.profile` with useful command aliases for each node user to easily manage their service.

## Getting Started

### Prerequisites

- Ansible installed on your local machine.
- An SSH key pair, with the public key added to the `authorized_keys` file on your server for the `root` user.

### 1. Clone the Repository

```bash
git clone https://github.com/CrazySerGo/ansible-oasis-node.git
cd ansible-oasis-node
```

### 2. Configure Your Inventory

Copy the example inventory and edit it to define your server and nodes.

```bash
cp inventory/hosts.yml.example inventory/hosts.yml
nano inventory/hosts.yml
```

In `inventory/hosts.yml`, you will need to:
- Set `ansible_host` to your server's IP address.
- Set `ansible_ssh_private_key_file` to the path of your private SSH key.
- For validator and runtime nodes that you wish to register, set the `entity_id`.

## Usage

All commands are run from your local machine, inside the `ansible-oasis-node` directory.

### Step 1: Installation (Client Mode)

The playbook defaults to installing all nodes in `client` mode for a safe initial sync.

**Install a single node:**
```bash
# Install a Mainnet validator
ansible-playbook playbook.yml --limit mainnet-validator --tags install

# Install a Testnet Sapphire node
ansible-playbook playbook.yml --limit testnet-sapphire --tags install
```

**Install a mixed-runtime node:**
This example installs a node that runs both the Cipher and Sapphire ParaTimes.
```bash
ansible-playbook playbook.yml --limit mainnet-mix --tags install
```

### Step 2: Switch to Operational Mode

Once your node is fully synced, you can switch it to its final operational mode.

**Switch to a Validator:**
```bash
ansible-playbook playbook.yml --limit mainnet-validator -e "node_mode=validator" -e "consensus_validator=true"
```

**Switch a runtime node to Compute:**
```bash
ansible-playbook playbook.yml --limit mainnet-mix -e "node_mode=compute"
```

### Management

You can use Ansible tags to perform specific management tasks.

**Update Node Binary:**
Checks for a new recommended version of Oasis Core and upgrades the binary if one is found.
```bash
# Update a single node
ansible-playbook playbook.yml --limit mainnet-validator --tags update_binary

# Update all Testnet nodes
ansible-playbook playbook.yml --limit testnet --tags update_binary
```

**Run Multiple Tasks:**
You can combine tags to run multiple tasks at once. For example, to update the configuration and then check the status:
```bash
ansible-playbook playbook.yml --limit mainnet-validator --tags update_config,status
```

## On-Server Management

After you SSH into the node as the service user (e.g., `ssh validator@<your_server_ip>`), you will have access to several helpful command aliases:

- `start-node`: Start the Oasis Node service.
- `stop-node`: Stop the Oasis Node service.
- `restart-node`: Restart the Oasis Node service.
- `logs`: View the live journald logs for the node.
- `status-service`: Check the status of the systemd service.
- `status-full`: Get the full JSON status output from the running node.
- `status-short`: Get a brief summary of the node's status.
- `hint`: Display a list of all available aliases.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
