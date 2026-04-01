Connector: Subnet Router
Introduction
Subnet Routers work by advertising routes to the tailnet. The routes are the CIDR blocks that you need to make available to the tailnet. A single Subnet Router can advertise more than one route. Tailscale's access controls (which we will learn more about later) allow you to enable to access to the entire list of routes or a subset of them.

To use a subnet router, first a Tailscale device must be configured to advertise routes, then other devices must be configured to accept routes, and lastly the tailnet's access controls must permit access to the routes.

Step 1: Advertise Routes
👈 On the remote VM run the command below to advertise the a common private IP range:

shell

copy

run
tailscale set --advertise-routes=10.0.0.0/8
You should see output like:

shell
$ tailscale set --advertise-routes=10.0.0.0/8
$
ℹ️ By default, the Tailscale controlplane does not allow just any device to advertise routes (as in the case of Subnet Routers and App Connectors). The advertised routes must be approved - either manually, via the API, or via Auto Approvers. Earlier, we configured auto approvers to simplify this exercise. We'll learn more about this later.

Step 2: Use the advertised routes
Unlike Exit Nodes, clients do not select which subnet router to use to access advertise routes. Clients are configured to accept routes and the Tailscale controlplane assigns a subnet router to the client from the available subnet routers for each advertised route. This means the controlplane handles load balancing and regional routing of subnet routers automatically.

GUI Configuration
Open the Settings panel from the Tailscale GUI.
Ensure Use Tailscale subnets is enabled.
CLI configuration
As we learned in the Client Configuration exercise, settings can be configured with the tailscale CLI as well.

💻 On your laptop, run the command below to list available exit nodes on the tailnet:

shell

copy
tailscale set --accept-routes
You should see output like below.

shell
$ tailscale set --accept-routes
$
Step 3: Confirm the routes are accessible
The subnet router is making a network segment available to the tailnet, so the Tailscale client does not provide a list of available IP addresses (16,777,214 IP addresses in the case of 10.0.0.0/8). Instead, you must know the IP address of the network resource you need to connect to.

👈 On the remote VM, run the command below to determine its private LAN IP address:

shell

copy

run
ip route get 1 | awk '{print $7; exit}'
You should see output like below.

shell
$ ip route get 1 | awk '{print $7; exit}'
10.32.0.204
$
Copy the output to your clipboard (e.g. 10.32.0.204 in the example above).

💻 On your laptop, ping the private IP with the command below.

shell

copy
ping IP
You should see output like below.

shell
$ ping 10.32.0.204
PING 10.32.0.204 (10.32.0.204): 56 data bytes
64 bytes from 10.32.0.204: icmp_seq=0 ttl=64 time=256.022 ms
64 bytes from 10.32.0.204: icmp_seq=1 ttl=64 time=222.436 ms
64 bytes from 10.32.0.204: icmp_seq=6 ttl=64 time=225.954 ms
^C
--- 10.32.0.204 ping statistics ---
7 packets transmitted, 7 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 222.139/235.311/270.799/18.301 ms
$
Hit CTRL+C to stop the ping.

You can also access the NGINX web server on the private IP.

shell

copy
curl IP
You should see output like:

shell
$ curl 10.32.0.204
Succesful response from NGINX.
$
You can also use tailscale ping ... to confirm the connector that is being used. This can be helpful when you have multiple connectors deployed for load balancing and/or regional routing and need to confirm or troubleshoot connectivity.

💻 On your laptop, run the command below to initiate a connection through the subnet router:

shell

copy
tailscale ping IP
You should see output like below.

shell
$ tailscale ping 10.32.0.204
pong from workshop (100.88.251.60) via 35.188.4.128:36608 in 221ms
$
Conclusion
You should now have an understanding of:

✅ How to configure a device to advertise routes to be a subnet router
✅ How to configure a device to accept routes to use a subnet router
✅ How to confirm connectivity
