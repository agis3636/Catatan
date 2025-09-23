auto vmbr1
iface vmbr1 inet static
        address 192.168.100.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0

#NAT SETTINGS
post-up echo 1 > /proc/sys/net/ipv4/ip_forward
post-up iptables -t nat -A POSTROUTING -s '192.168.100.0/24' -o vmbr0 -j MASQUERADE
post-down iptables -t nat -D POSTROUTING -s '192.168.100.0/24' -o vmbr0 -j MASQUERADE
