Connectors
So far we've demonstrated device-to-device connectivity between two devices that have Tailscale installed directly on them.

There are other ways to connect network resources in Tailscale. We generally call these connectors...

Exit Node
For routing Internet-bound traffic through a remote device. This most closely resembles traditional full-tunnel VPNs. This is ideal for:

Securing internet traffic while on an untrusted network such as a hotel or coffee shop WiFi
Routing traffic through a remote location for accessing location-restricted or geoblocked services
Subnet Router
For connecting to devices in a network that you cannot, or choose not to, install Tailscale on. This is ideal for connecting to:

Pre-existing servers that you do not want to change or add software to
Appliances or devices that do not allow custom software
Managed cloud services such as AWS RDS, Azure SQL, Google Cloud SQL, etc.
App Connector
For connecting to third-party SaaS products that support IP allowlisting for security. This is ideal for connecting to:

Any third-party SaaS such as GitHub, Salesforce, Zendesk, etc.
Step 1: Enable IP Forwarding
IP forwarding is required to use a Linux device as an exit node, subnet router, and app connector. This kernel setting lets the system forward network packets between interfaces, essentially functioning as a router.

👈 On the remote VM enable IP forwarding with the commands below:

shell

copy

run
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
You should see output like:

shell
$ echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
$
Step 2: Configure access controls
We will go into much more detail about access controls in a later exercise. To prepare for some of the upcoming exercises, we need to set some other settings for now.

Open the admin console then click the Access controls panel if not already open.
Click the JSON editor toggle.
Select the entire text in the Edit file editor (click into it, then hit CTRL+A or CMD+A then hite DELETE.
Copy the JSON below and paste into the Edit file editor.
Scroll to the bottom of the page and click Save.
json

copy
// Example/default ACLs for unrestricted connections.
{
	// Declare static groups of users. Use autogroups for all users or users with a specific role.
	// "groups": {
	//   "group:example": ["alice@example.com", "bob@example.com"],
	// },

	// Define the tags which can be applied to devices and by which users.
	// "tagOwners": {
	//   "tag:example": ["autogroup:admin"],
	// },

	// Define grants that govern access for users, groups, autogroups, tags,
	// Tailscale IP addresses, and subnet ranges.
	"grants": [
		// Allow all connections.
		// Comment this section out if you want to define specific restrictions.
		{
			"src": ["*"],
			"dst": ["*"],
			"ip":  ["*"],
		},

		// Allow users in "group:example" to access "tag:example", but only from
		// devices that are running macOS and have enabled Tailscale client auto-updating.
		// {"src": ["group:example"], "dst": ["tag:example"], "ip": ["*"], "srcPosture":["posture:autoUpdateMac"]},
	],

	// Define postures that will be applied to all rules without any specific
	// srcPosture definition.
	// "defaultSrcPosture": [
	//      "posture:anyMac",
	// ],

	// Define device posture rules requiring devices to meet
	// certain criteria to access parts of your system.
	// "postures": {
	//      // Require devices running macOS, a stable Tailscale
	//      // version and auto update enabled for Tailscale.
	//  "posture:autoUpdateMac": [
	//      "node:os == 'macos'",
	//      "node:tsReleaseTrack == 'stable'",
	//      "node:tsAutoUpdate",
	//  ],
	//      // Require devices running macOS and a stable
	//      // Tailscale version.
	//  "posture:anyMac": [
	//      "node:os == 'macos'",
	//      "node:tsReleaseTrack == 'stable'",
	//  ],
	// },

	// Define users and devices that can use Tailscale SSH.
	"ssh": [
		// Allow all users to SSH into their own devices in check mode.
		// Comment this section out if you want to define specific restrictions.
		{
			"action": "check",
			"src":    ["autogroup:member"],
			"dst":    ["autogroup:self"],
			"users":  ["autogroup:nonroot", "root"],
		},
	],

	"autoApprovers": {
		"routes": {
			"::/0":  ["autogroup:admin", "tag:connector"],
			"0.0.0.0/0":  ["autogroup:admin", "tag:connector"],
			"10.0.0.0/8": ["autogroup:admin", "tag:connector"],
		},
	},

	"tagOwners": {
		// tag for exit nodes, subnet routers, and app connectors
		"tag:connector": ["autogroup:admin"],
	},

	// Test access rules every time they're saved.
	// "tests": [
	//   {
	//       "src": "alice@example.com",
	//       "accept": ["tag:example"],
	//       "deny": ["100.101.102.103:443"],
	//   },
	// ],
}
Don't worry about the details of this file for now. We'll come back to it in a later exercise.

Step 3: Tag the remote VM
So far, the remote VM has been authenticated as you. To use the remote VM as a connector we should tag it. This will remove your identity from it and turn it into a tagged device which is more appropriate in real-world environments as we'll learn in a bit.

In the admin console click into the workshop device (or whichever you renamed it to).
Then click ⚙︎ Machine Settings then Edit ACL tags...
Click the Add tags dropdown, select tag:connector and click Save.
You'll notice the machine now says Managed by tag:connector instead of your email address.

We'll come back to this in a bit to explain tags and tagged devices in more detail.
