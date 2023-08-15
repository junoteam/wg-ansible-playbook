# Ansible WireGuard

This Ansible script install a Wireguard VPN server on top of AML2023.

## Requirement

On your client laptop:
- Install [WireGuard](https://www.wireguard.com/install/)
- Generate client's private and public keys:
  ```bash
  umask 077
  wg genkey | tee privatekey | wg pubkey > publickey
  ```
- Add this key to server config and bring down/up wg interface `/etc/wireguard/wg0.conf`

## Usage

The following script does the job to make for example a VPS as a VPN server. Your WireGuard client public key will be asked.

```bash
cd ~
git clone git@github.com:junoteam/wg-ansible-playbook.git wg-ansible-playbook/
ansible-playbook ansible-wireguard/enable_iptables.yml
ansible-playbook ansible-wireguard/wireguard_server.yml
```

Add WG config `wg0.conf` into your client on MacOS or Linux:

```bash
# local device
[Interface]
Address = 10.0.0.2/32
PrivateKey = <your client private key>
DNS = 1.1.1.1

# server
[Peer]
PublicKey = <the server public key given by ansible script>
Endpoint = <IP>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```
