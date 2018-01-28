# Packer CentOS for ESXI

Packer build for CentOS VMs on ESXI.

## Configure ESXI

First, configure the ESXI host to allow Packer to find IP addresses of VMs:

```bash
esxcli system settings advanced set -o /Net/GuestIPHack -i 1
```

Next, open VNC ports on the ESXI host firewall:

```bash
chmod 644 /etc/vmware/firewall/service.xml
chmod +t /etc/vmware/firewall/service.xml
vi /etc/vmware/firewall/service.xml
```

Add this to bottom of the file (above </ConfigRoot>): 

```xml
<service id="1000">
  <id>packer-vnc</id>
  <rule id="0000">
    <direction>inbound</direction>
    <protocol>tcp</protocol>
    <porttype>dst</porttype>
    <port>
      <begin>5900</begin>
      <end>6000</end>
    </port>
  </rule>
  <enabled>true</enabled>
  <required>true</required>
</service>
```

Reload the firewall:

```bash
chmod 444 /etc/vmware/firewall/service.xml
esxcli network firewall refresh
```

## Pre-Build

For the ISO, either add it to the `./iso` directory or let Packer pull it remotely.

Set the following environment variables to provide access to the remote ESXI host:

```bash
export ESXI_HOST='<IP ADDRESS>'
export ESXI_DATASTORE='<DATASTORE NAME>'
export ESXI_USERNAME='<SSH USERNAME>'
export ESXI_PASSWORD='<SSH PASSWORD>'
```

## Build

```bash
packer build centos-7-base.json
```
