Connector: Exit Node
Introduction
Exit Nodes work by advertising a default route (0.0.0.0/0 and ::/0) to the tailnet. There can be multiple exit nodes on the tailnet. Other devices be explicitly configured to use an exit node. Tailscale's access controls (which we will learn more about later) allow you to enable access to all exit nodes, or a subset of them, to other devices on the tailnet.

To use an exit node, first a Tailscale device must be configured to advertise as an exit node, then other devices must be configured to use the exit node, and lastly the tailnet's access controls must permit the exit node to be used.

Step 1: Advertise Exit Node
👈 On the remote VM enable advertising the device as an Exit Node:

shell

copy

run
tailscale set --advertise-exit-node
You should see output like:

shell
$ tailscale set --advertise-exit-node
$
By default, the Tailscale controlplane does not allow any device to become an exit node. The device must be approved to be used as an exit node - either manually, via the API, or via Auto Approvers which we will learn more about later. For now we will approve it manually...

In the admin console go to the Machines page and click into the remote VM. Under Routing Settings you should see Exit Node: Awaiting Approval. Click Edit, click the checkbox next to Use as exit node, then click Save.

Step 2: Use the Exit Node
GUI Configuration
💻On your laptop:

In the Tailscale menu open the Exit Nodes menu.
Under Tailscale Exit Nodes select the remote VM.
The Tailscale app icon will change to include an arrow indicating the exit node has been selected. Now all your Internet-bound traffic, including Google, Zoom, and the Instruqt training website is routing through the selected exit node.

⚠️ Since the remote VM runs in Instruqt's cloud environment, this can acuse issues with connectivity to Instruqt as well as the Admin Console. If you receive an error from Instruqt, the Theilscale Tailscale Admin Console, or other sites, repeat the instructions above to select None as the exit node.

CLI configuration
As we learned in the Client Configuration exercise, settings can be configured with the tailscale CLI as well.

💻 On your laptop, run the command below to list available exit nodes on the tailnet:

shell

copy
tailscale exit-node list
You should see output like below.

shell
$ tailscale exit-node list

 IP                 HOSTNAME                                 COUNTRY     CITY      STATUS
 100.111.174.16     remote-workstation.tail6c02e5.ts.net     -           -         -

# To view the complete list of exit nodes for a country, use `tailscale exit-node list --filter=` followed by the country name.
# To use an exit node, use `tailscale set --exit-node=` followed by the hostname or IP.
# To have Tailscale suggest an exit node, use `tailscale exit-node suggest`.
$
Confirm the exit node is in use
Unless a website or app is restricting access to the egress IP of the remote VM (which can be the case for cloud IP addresses), you shouldn't really notice if your traffic is now routing through the remote VM. We can use an IP lookup service like ipinfo.io to confirm that our traffic is now routing through the remote VM.

💻 On your laptop, run the command below to use ipinfo.io to lookup your egress IP when the Exit Node is in use:

shell

copy
curl ipinfo.io
You should see output like below.

shell
$ curl ipinfo.io
{
  "ip": "34.143.173.95",
  "hostname": "95.173.143.34.bc.googleusercontent.com",
  "city": "Singapore",
  "region": "Singapore",
  "country": "SG",
  "loc": "1.2897,103.8501",
  "org": "AS396982 Google LLC",
  "postal": "018989",
  "timezone": "Asia/Singapore",
  "readme": "https://ipinfo.io/missingauth"
}%
$
Suggested exit nodes
Tailscale can suggest an exit node to use based on observed latency between your device and the available exit nodes.

💻 On your laptop, run the command below to see the suggested exit node:

shell

copy
tailscale exit-node suggest
You should see output like below.

shell
$ tailscale exit-node suggest
Suggested exit node: remote-workstation.tail6c02e5.ts.net.
To accept this suggestion, use `tailscale set --exit-node=remote-workstation.tail6c02e5.ts.net.`.
$
💻 On your laptop, copy the command from the suggest output to configure your laptop to use the remote VM as an exit node:

shell

copy
tailscale set --exit-node=...
You should see output like below.

shell
$ tailscale set --exit-node=remote-workstation.tail6c02e5.ts.net.
$
Use a suggested exit node automatically
Devices can be configured to use a suggested exit node automatically. This is typically done for users or devices that are expected to always use an exit node such as a support team or remote devices on untrusted networks. Typically the device would be pre-configured via MDM or a configuration management tool to use the suggested exit node automatically.

💻 On your laptop, run the command below to set your laptop to use the suggested exit node automatically.

shell

copy
tailscale set --exit-node=auto:any
You should see output like below.

shell
$ tailscale set --exit-node=auto:any
$
Reset
💻 On your laptop, run the command below to set your laptop to no longer use the exit node. Or use the Exit Nodes GUI menu and select None.

shell

copy
tailscale set --exit-node=
You should see output like below.

shell
$ tailscale set --exit-node=
$
Conclusion
You should now have an understanding of:

✅ How to configure a device to be an exit node
✅ How to configure a device to use an exit node
✅ How to use suggested exit nodes
✅ How to reset a device from using an exit node
✅ When to use exit nodes
