# Enable SSH Access into VirtualBox VM

Open your VM **Settings** -> **Network** tab -> Change Adapter into **Bridged
Adapter**.

![Bridged Adapter](../images/bridged-network.png)

Start the VirtualMachine then ensure the ssh service is running.

```bash
systemctl status sshd
```

Get the VirtualMachine's IP using following command.

```bash
ip addr
```

then run ssh command to connect using `VM`'s IP.

```bash
ssh user@vm-ip
```

## Recommended SSH client
- [Termius](https://termius.com/)
- [Putty](https://www.putty.org/)
