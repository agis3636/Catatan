Access Controls
Introduction
Tailscale's access control system is deny-by-default, meaning all access must be granted. For ease of use, a new Tailscale network (tailnet) initializes with a default "allow all" policy. So far all of our testing has been working due to this "allow all" policy.

ℹ️ Tailscale policy file and access controls have evolved over time. Grants are Tailscale's enhanced access control system that combines network layer and application layer permissions into a unified framework. ACLs are the legacy, but still supported, access control system. Grants and legacy ACLs can coexist in the same tailnet policy file. The recommended best practice is to favor grants for most use cases.

Grants (and other settings) are defined in a single policy file. This policy file can be manage in the admin console, via the API, with Infrastructure-as-Code (e.g. Terraform and Pulumi) and with GitOps. For this exercise we will make changes in the admin console.

Step 1: Remove access
To understand how changes to the policy file take effect, let's revoke the "allow all" policy.

Open the admin console then click the Access controls panel if not already open, then click the Visual editor toggle.
Click the All users and devices row to see details of the access rule. Note that Source, Destination, and ``Port and protocolare all*`. This is the "allow all" policy.
Click the ... menu, then click Delete.
Step 2: Confirm no connectivity
Tailscale's acccess rules define access, and therefore visibility, to other network resources. If a rule does not exist that gives you access to a particular port and protocol for a device, then your device will not be able to see the other device exists at all.

💻 On your laptop, run the command below to see other nodes on the tailnet:

shell

copy
tailscale status
You should see output like below that lists only your laptop.

shell
$ tailscale status
100.120.125.20  cameron-macbookair-tailscale  cameron@  macOS  -
$
Step 3: Enable access to tag:connector devices
Tailscale's access rules distinguish between access to a device and access through a device as in the case of a connector such as an exit node, subnet router, and app connector.

To enable access to the VM

In the admin console click + Add Rule.
Enter * as the Source, tag:connector as the Destination, leave the rest of the values as-is, and click Save grant.
This will enable connectivity to the remote VM and any future devices tagged with tag:connector that join the tailnet.

Step 4: Test device connectivity
Confirm connectivity to the VM
💻 On your laptop, run the command below to see other nodes on the tailnet:

shell

copy
tailscale status
You should see output like below that lists the remote VM again.

shell
$ tailscale status
100.120.125.20  cameron-macbookair-tailscale  cameron@        macOS  -
100.88.251.60   workshop                      tagged-devices  linux  -
$
You should again be able to connecto the NGINX web server.

💻 On your laptop, run the command below to test connectivity to the device:

shell

copy
curl IP
You should see output like:

shell
$ curl 100.88.251.60
Succesful response from NGINX.
$
Confirm no connectivity to the subnet or app connector
So far we have only re-enabled access to the VM itself, not any routes through it.

Repeat the step above, but use the LAN IP this time and you should get an error.

💻 On your laptop, run the command below to see test connectivity through the subnet router:

shell

copy
curl --connect-timeout 5 LAN_IP
You should see output like:

shell
$ curl 10.32.0.204
curl: (28) Failed to connect to 10.32.0.204 port 80 after 5003 ms: Timeout was reached
$
Step 5: Enable access through the subnet router
Because Tailscale's access rules distinguish between access to a device and access through a device we need to define access rules for acces through the subnet router.

To enable access to the advertised routes:

In the admin console click + Add Rule.
Enter * as the Source, 10.0.0.0/8 as the Destination, leave the rest of the values as-is, and click Save grant.
Step 6: Test subnet router connectivity
💻 On your laptop, run the command below to see test connectivity through the subnet router:

shell

copy
curl LAN_IP
You should see output like:

shell
$ curl 10.32.0.204
Succesful response from NGINX.
$
Step 7: Enable access through the exit node
So far we've enabled access to the device and the advertised routes, but we're still unable to use the remote VM as an exit node. To allow the device to be used as an exit node, we need to enable access to 0.0.0.0/0 and ::/0 (i.e. the internet). But, those routes include all IP addresses - the internet and private addresses. Tailscale provides autogroups - special groups that automatically includes users, destinations, or usernames with the same properties. In particular, Tailscale provides a autogroup:internet which includes all Internet-bound routes, but exclues private IPs and Tailscale's CGNAT range.

To allow the device to be used as an exit node:

In the admin console click + Add Rule.
Enter * as the Source, autogroup:internet as the Destination, leave the rest of the values as-is, and click Save grant.
Step 8: Test exit node connectivity
💻 On your laptop, run the command below to see the VM as an available exit node again.

shell

copy
tailscale exit-node list
You should see output like:

shell
$ tailscale exit-node list

 IP                 HOSTNAME                                 COUNTRY     CITY      STATUS
 100.111.174.16     remote-workstation.tail6c02e5.ts.net     -           -         -

# To view the complete list of exit nodes for a country, use `tailscale exit-node list --filter=` followed by the country name.
# To use an exit node, use `tailscale set --exit-node=` followed by the hostname or IP.
# To have Tailscale suggest an exit node, use `tailscale exit-node suggest`.
$
Step 9: Enable access through the app connector
We've already enabled access to the remote VM and through the remote VM to autogroup:internet so our app connector access should be working again.

Step 10: Test app connector connectivity
💻 On your laptop, run the command below to see which app connector your laptop is connecting through:

shell

copy
tailscale ping ipinfo.io
You should see output like below.

shell
$ tailscale ping ipinfo.io
pong from workshop (100.88.251.60) via 35.188.4.128:36608 in 264ms
$
JSON editor
So far we've been using the Visual editor to make policy file changes. The visual editor has been editing the underlying JSON policy file as we've been making changes.

Open the admin console then click the Access controls panel if not already open.
Click the JSON editor toggle to see the policy file in JSON form.
Some users prefer to use the visual editor and some prefer to use the JSON editor. Companies that have embraced Infrastrure-as-Code and GitOps may prefer to manage their policy file entirely as code.

User & Group Access
So far we've enabled access from * which means all devices, users, and groups. In real-world situations you would likely want to limit the sources further.

User access
You can reference users directly in your policy file like below:

json

copy
{
  "grants": [
    {
      "src": ["alice@example.com", "bob@example.com"],
      "dst": ["tag:connector"],
      "ip": ["*"]
    }
  ],
}
Group access
The Tailscale SaaS supports user & group provisioning which allows companies to automatically synchronize their users and group membership information from their identity provider to Tailscale.

You can use the same human-readable group names you have in a SCIM-integrated identity provider to refer to groups in your access control policies. For example, if Alice and Bob are in the "security-team" group in a SCIM-integrated identity provider, then the access rule can refer to the "security-team" group:

json

copy
{
  "grants": [
    {
      "src": ["group:security-team@example.com"],
      "dst": ["tag:connector"],
      "ip": ["*"]
    }
  ],
}
Conclusion
You should now have an understanding of:

✅ Using the admin console to make ACL and policy files changes
✅ Enabling connectivity to a set of devices using tags
✅ Enabling connectivity to network resources behind a subnet router
✅ Enabling connectivity through an exit node
✅ Enabling connectivity through an app connector
✅ Enabling connectivity based on SCIM-provisioned users & groups
