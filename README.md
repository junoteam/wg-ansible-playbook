# Ansible WireGuard
This Ansible playbook to install a Wireguard VPN server on top of AML2023.

## Requirement

On your client laptop:
- Install [WireGuard](https://www.wireguard.com/install/)
- Generate client's private and public keys:
  ```bash
  umask 077
  wg genkey | tee privatekey | wg pubkey > publickey
  ```
- Add this key to server config `/etc/wireguard/wg0.conf` and bring down/up wg interface.

## If you want to run it manually on server follow -->

## Clone the repo
```bash
cd ~
git clone git@github.com:junoteam/wg-ansible-playbook.git wg-ansible-playbook/
```

## Install and enable IPtables
```bash
ansible-playbook wg-ansible-playbook/playbooks/enable_iptables.yml

[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Setup iptables and install git on server] ***************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
ok: [localhost]

TASK [Update all packages] ************************************************************************************************************************************************
ok: [localhost]

TASK [Install iptables-services] ******************************************************************************************************************************************
changed: [localhost]

TASK [Enable and start iptables service] **********************************************************************************************************************************
changed: [localhost]

PLAY RECAP ****************************************************************************************************************************************************************
localhost                  : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## Install and enable Wireguard
```bash
ansible-playbook wg-ansible-playbook/playbooks/wireguard_server.yml

PLAY [Setup Wireguard Server] *********************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
ok: [localhost]

TASK [Set ipv4 Forwarding] ************************************************************************************************************************************************
ok: [localhost]

TASK [Make sysctl changes to be permament] ********************************************************************************************************************************
ok: [localhost]

TASK [Update all packages] ************************************************************************************************************************************************
ok: [localhost]

TASK [Import WireGuard keys] **********************************************************************************************************************************************
ok: [localhost]

TASK [Add WireGuard repository] *******************************************************************************************************************************************
ok: [localhost]

TASK [Install WireGuard tools] ********************************************************************************************************************************************
ok: [localhost]

TASK [mkdir -p /etc/wireguard/keys] ***************************************************************************************************************************************
ok: [localhost]

TASK [umask 077] **********************************************************************************************************************************************************
changed: [localhost]

TASK [Creating server privatekey and publickey] ***************************************************************************************************************************
changed: [localhost]

TASK [cat privatekey => var_privatekey] ***********************************************************************************************************************************
changed: [localhost]

TASK [Creating /etc/wireguard/wg0.conf] ***********************************************************************************************************************************
changed: [localhost]

TASK [Starting wg service] ************************************************************************************************************************************************
changed: [localhost]

TASK [Check server public IP] *********************************************************************************************************************************************
changed: [localhost]

TASK [cat publickey => var_publickey] *************************************************************************************************************************************
changed: [localhost]

TASK [debug] **************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Server public IP is 54.82.174.84, Server public key is o8y7B6wtEKaKNeUAQrCO7seAIEqqXPwKOE1kN/eBfFk="
}

PLAY RECAP ****************************************************************************************************************************************************************
localhost                  : ok=16   changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
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
