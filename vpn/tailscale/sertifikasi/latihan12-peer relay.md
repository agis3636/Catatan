Peer Relay
Introduction
Tailscale Peer Relays let you use devices in your Tailscale network (known as a tailnet) as high-throughput relay servers when direct connections aren't possible due to network conditions such as a strict NAT environment.

Normally, Tailscale relies on its globally distributed DERP servers to relay traffic when Tailscale can't establish direct connections between devices. While DERP servers are effective at relaying traffic, they can introduce additional latency, especially for high-throughput applications. When you configure a device as a peer relay, other devices in the same tailnet can use it to relay traffic for client-to-client connections when a direct connection isn't possible. Tailscale first attempts to use any available peer relays in the tailnet before falling back to DERP servers.

Step 1: Enable peer relay
First, a Tailscale device must be designated as a peer relay.

☁️ In the terminal for the remote VM run the command below and then click the link in the output to authenticate the VM to your tailnet:

shell

copy

run
tailscale set --relay-server-port=4444
You should see output like:

shell
$ tailscale set --relay-server-port=4444
$
ℹ️ You can deploy multiple peer relays for high-availabilty and load balancing.

Step 2: Create a peer relay policy
Before devices in the tailnet can use devices you designate as peer relays, you need to create a grant policy that lets them do so. The grant policy uses the tailscale.com/cap/relay application capability to specify which devices can use the peer relay functionality.

Open the admin console then click the Access controls panel if not already open.
Click the JSON editor toggle.
Copy the JSON below and paste inside the "grants" section of the policy file.
json

copy
{
  "src": ["*"], // Devices that can be accessed through the peer relay
  "dst": ["tag:connector"], // Devices functioning as peer relays for the src devices
  "app": {
    "tailscale.com/cap/relay": [] // The relay capability doesn't require any parameters
  }
}
Step 3: Confirm connectivity
⚠️ If you've been getting direct connections all this time, it may be hard to confirm connectivity with tailscale ping.

💻 On your laptop, use tailscale ping to ping the remote VM.

shell

copy
tailscale ping IP
A peer relayed connection will look like the output below.

shell
$ tailscale ping 100.115.191.106
pong from workshop (100.115.191.106) via DERP(sea) in 183ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 222ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 218ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 253ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 220ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 218ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 315ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 228ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 218ms
pong from workshop (100.115.191.106) via peer-relay(54.184.20.229:7777:vni:12198) in 221ms
2026/03/29 22:37:54 direct connection not established
#
$
You can also confirm the list of peer relays available to a device with the tailscale debug peer-relay-servers command like below.

shell
$ tailscale debug peer-relay-servers
[
  "100.88.251.60"
]
$
Conclusion
You should now have an understanding of:

✅ When to use a peer relay
✅ How to configure a peer relay
✅ How to configure the tailnet to allow other devices to use the peer relay
