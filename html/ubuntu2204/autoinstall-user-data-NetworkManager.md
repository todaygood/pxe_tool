

## 1. 设置NetworkManager 

ENI: using ifup, ifdown to manage device setup/teardown

Netplan: using netplan apply to manage device setup/teardown

NetworkManager: using nmcli to manage device setup/teardown

Networkd: using ip to manage device setup/teardown




autoinstall:
  version: 1
  identity:
    hostname: ubuntu-server
    username: user
    password: password
  locale: en_US.UTF-8
  keyboard:
    layout: en
  timezone: UTC
  updates:
    - apt
  storage:
    config:
      - {name: disk1, ptype: disk, grub_device: true, preserve: false, wipe: superblock, type: disk, part_id: 1, size: 0, ptable: gpt, path: /dev/sda, flags: [bios_grub]}
      - {ptype: part, name: root, size: '100%'}
      - {fstype: ext4, volume_id: root, device: disk1, part_id: 2, mountpoint: /}
      # Enable snaps and install network-manager
  packages:
    - network-manager
  network:
    version: 2
    renderer: NetworkManager
    ethernets:
      enp0s3:
        dhcp4: no
        addresses: [192.0.2.10/24]
        gateway4: 192.0.2.1
        nameservers:
          addresses: [8.8.8.8, 8.8.4.4]
  late-commands:
    - curtin in-target -- systemctl enable --now snap.network-manager.networkmanager.service
  ssh:
    install_server: true


## 设置root密码

https://askubuntu.com/questions/1503381/update-set-root-password-in-auto-install

#cloud-config
autoinstall:
  user-data:
    chpasswd:
      expire: false
      list:
        - root:$6$REDACTED


#cloud-config
autoinstall:
  late-commands:
    - |
      echo 'root:$6$REDACTED' | /usr/sbin/chroot /target chpasswd -e


## 设置sshd允许root使用密码登录


## 注入ssh key

## timezone 

## 分区

## 自动配置apt源 

https://canonical-subiquity.readthedocs-hosted.com/en/latest/reference/autoinstall-reference.html#network

https://cloudinit.readthedocs.io/en/latest/reference/network-config.html


https://www.molnar-peter.hu/en/ubuntu-jammy-netinstall-pxe.html

https://punkto.org/blog/ubuntu_22_autobuilding

https://github.com/ziwend/pxe_tool