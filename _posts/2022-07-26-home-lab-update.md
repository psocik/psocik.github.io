# Home Lab Update

Hi,

recently I have reinstalled my homelab servers. Previously there were 2x intel nuc devices with windows server 2019 Datacentre and Standard editions installed. Now I moved to proxmox 7.2 hypervisor.

## Current state

Currently I finished installing services:

1. 2x hypervisors Proxmox setup as one cluster.
2. pfsense as main firewall for lab environment.
3. pihole as DNS server for lab.
4. docker for container services.
5. Nginx Proxy Manager for managing external services.
6. Opnsense as additional firewall/IDS service.
7. ELK stack for central log management and XDR service.

Additionally, 3 windows 10 VMs for client traffic running some common software.

### Things I have to work on

TODO:

1. Configure fleet services in ELK stack.
2. Setup PI Alert
3. Setup Windows Server Datacenter for ADDS and other domain services.
4. More clients vms - Linux and Windows for dedicated services

After that plan is to start posting about:

1. Configuring AD services including setting up cloud shared services.
2. Implementing and monitoring network in lab and also in servers and client.

Stay tuned :)