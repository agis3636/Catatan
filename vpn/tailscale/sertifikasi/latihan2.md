# Connectivity
Prequisites
Ensure you completed the previous exercise and have:

Tailscale authenticated on your laptop
Tailscale authenticated on the remote workshop VM
## Step 1: Tailscale Status
👈 In the terminal for the remote VM, run the command below to see the status of Tailscale and all nodes visible to the remote VM:

```bash
run
tailscale status
You should see output like:

shell
$ tailscale status
100.124.53.54   workshop                      cameron@  linux  -
100.120.125.20  cameron-macbookair-tailscale  cameron@  macOS  -
$
```

💻 On your laptop, run the same tailscale status command. You should see similar output.

## Step 2: Ping
💻 On your laptop, copy the 100.x.y.z IP address for the workshop device and then ping the IP:

```bash
ping IP
You should see output like below. CTRL+C to cancel the ping.

shell
$ ping 100.124.53.54
PING 100.124.53.54 (100.124.53.54): 56 data bytes
64 bytes from 100.124.53.54: icmp_seq=0 ttl=64 time=52.707 ms
64 bytes from 100.124.53.54: icmp_seq=1 ttl=64 time=21.102 ms
64 bytes from 100.124.53.54: icmp_seq=2 ttl=64 time=23.917 ms
64 bytes from 100.124.53.54: icmp_seq=3 ttl=64 time=22.138 ms
64 bytes from 100.124.53.54: icmp_seq=4 ttl=64 time=25.994 ms
^C
--- 100.124.53.54 ping statistics ---
5 packets transmitted, 5 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 21.102/29.172/52.707/11.885 ms
$
```
Hit CTRL+C to stop the ping.

🎉 You've now confirmed connectivity between the remote VM and your local laptop.

## Step 3: Tailscale ping
ℹ️ tailscale ping will provide additional information about connectivity between devices.

💻 On your laptop, copy the 100.x.y.z IP address for the workshop device and then ping the IP:

```bash
tailscale ping IP
You should see output like below. tailscale ping will stop after 10 pings or after a direct connection is achieved.

shell
$ tailscale ping 100.115.191.106
pong from workshop (100.115.191.106) via DERP(sin) in 79ms
pong from workshop (100.115.191.106) via DERP(sin) in 24ms
pong from workshop (100.115.191.106) via DERP(sin) in 23ms
pong from workshop (100.115.191.106) via 34.143.173.95:1024 in 22ms
$
```

ℹ️ Tailscale connects devices within a Tailscale network (known as a tailnet) using three connection types:

Direct connections: Devices send packets directly to each other using UDP.
DERP relayed connections: Devices send packets through DERP servers.
Peer Relay connections: Devices send packets through another Tailscale device in the same tailnet acting as a relay.
All three options are end-to-end encrypted with WireGuard. The primary difference between them is performance, not security. Direct connections usually provide the lowest latency and highest throughput, while relayed connections are a fallback when direct connectivity isn't possible.

🧑‍🏫 Tailscale uses NAT Traversal to create direct connections through NAT gateways without requiring open firewall ports. NAT traversal is an advanced topic. See How NAT traversal works for more details.

👈 On the remote VM, repeat the previous steps but ping the 100.x.y.z address of your laptop:

```bash
tailscale ping 100.120.125.20
You should see output like:

shell
$ tailscale ping 100.120.125.20
pong from cameron-macbookair-tailscale (100.120.125.20) via 103.76.14.224:41641 in 22ms
$
```

ℹ️ If you completed this step quickly after the previous tailscale ping then Tailscale likely already had the direct connection established and could skip the DERP connection. Tailscale will maintain direct connections for some time after they've been established.

🎉 You've now confirmed connectivity between the remote VM and your local laptop.

## Step 4: IPv6
Open the admin console then click into the remote VM and copy the Tailscale IPv6 value of the machine (i.e. workshop.tail6c02e5.ts.net).

💻 On your laptop, copy the 100.x.y.z IP address for the workshop device and then ping the IP:

```bash
tailscale ping IPv6
You should see output like below.

shell
$ tailscale ping fd7a:115c:a1e0::c501:bfc2
pong from workshop (100.115.191.107) via 34.143.173.95:1024 in 21ms
$
```

## Step 5: MagicDNS
MagicDNS is an optional feature (on by default) that provides human-friendly DNS names for all Tailscale devices.

Open the admin console.
In the Machines list click the remote VM.
Click the Full domain value to copy the value for the machine (e.g. workshop.tail6c02e5.ts.net).
💻 On your laptop ping the MagicDNS name like below:

```bash
tailscale ping DNS
You should see output like:

shell
$ tailscale ping workshop.tail6c02e5.ts.net
pong from workshop (100.115.191.106) via 34.143.173.95:1024 in 24ms
$
```

Hit CTRL+C to stop the ping if it does not stop automatically.

In the admin console, click into the workshop device, then click ⚙︎ Machine Settings, then Edit machine IPv4..., then enter a new IP address within 100.64.0.0/10.

💻 On your laptop repeat the ping using the MagicDNS name:

```bash
tailscale ping DNS
You should see similar output as before, but with the new IP address:

shell
$ tailscale ping workshop.tail6c02e5.ts.net
pong from workshop (100.115.191.107) via DERP(sin) in 77ms
pong from workshop (100.115.191.107) via DERP(sin) in 28ms
pong from workshop (100.115.191.107) via DERP(sin) in 24ms
pong from workshop (100.115.191.107) via 34.143.173.95:1024 in 24ms
$
```

🎉 You've now seen how MagicDNS can be helpful when connecting to other Tailscale devices. Tailscale IP address are stable. They should not change unless the device has to be reauthenticated or an administrator changes them. If the Tailscale IP address does change, the MagicDNS name will resolve to the new address.

## Step 6: HTTP
An HTTP server (NGINX) is already installed on your remote VM.

So far we've confirmed connectivity with ping and tailscale ping. Tailscale operates at Layer 3 so other protocols, such as HTTP, work too.

💻 On your laptop use curl to connect to the HTTP server on the remote VM:

```bash
curl DNS
You should see output like:

shell
$ curl workshop.tail6c02e5.ts.net
Succesful response from NGINX.
$
```

ℹ️ We did not have to open any firewall ports on the remote network to enable connectivity to the remove VM. Tailscale is able to forward all protocols (ping, HTTP, etc.) over its connections.

Conclusion
You should now have an understanding of:

✅ Tailscale's IPv4, IPv6 and MagicDNS addresses
✅ DERP and Direct connections types
✅ Connecting other protocols over Tailscale
