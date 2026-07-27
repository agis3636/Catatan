Connector: App Connector
Introduction
App Connectors work similarly to Subnet Routers in that they advertise routes to the tailnet. The routes can be discovered and advertised automatically based on clients on the network initiating requests to the configured domains. Or preset apps can be used to use static IP ranges for well known services such as GitHub, Salesforce, etc.

To use a subnet router, first a Tailscale device must be configured to advertise itself as an app connector, then the app is configured on the tailnet, then other devices must be configured to accept routes, and lastly the tailnet's access controls must permit access to the app connector and its routes.

Step 1: Advertise App Connector
👈 On the remote VM configure it to advertise as an app connector with the command below.

shell

copy

run
tailscale set --advertise-connector
You should see output like:

shell
$ tailscale set --advertise-connector
$
ℹ️ By default, the Tailscale controlplane does not allow just any device to advertise routes (as in the case of Subnet Routers and App Connectors). The advertised routes must be approved - either manually, via the API, or via Auto Approvers. Earlier, we configured auto approvers to simplify this exercise. We'll learn more about this later.

Step 2: Configure the app
We must now confgure the tailnet to handle the domain(s) for the app we want to secure with the app connector. We'll use IPinfo as our app as we used for testing the Exit Node earlier.

Open the admin console then click the Apps panel if not already open. Click Add app, enter ipinfo as the name, in Target dropdown select Custom, enter ipinfo.io in the textbox, under Connectors in the Select ACL tags dropdown select tag:connector, then click Save.

ℹ️ Many SaaS apps have many domains, subdmains, and wildcard domains. For example, GitHub uses github.com, *.api.github.com, *.github.dev, and many more. The Tailscale SaaS provides many of these as preset apps so end-user administrators do not have to maintain the list of domains themselves.

Step 3: Confirm the app is accessible
For custom apps (e.g. not preset apps), the app connector will learn which routes (IP addresses) to advertise based on other devices initiating connections to the domains configured for the app. For our app configured in the previous step, we just need to initiate a connection to ipinfo.io to use the app connector.

💻 On your laptop, run the command below to initiate a connection through the app connector:

shell

copy
curl ipinfo.io
You should see output like below.

shell
$ curl ipinfo.io
{
  "ip": "35.188.4.128",
  "hostname": "128.4.188.35.bc.googleusercontent.com",
  "city": "Council Bluffs",
  "region": "Iowa",
  "country": "US",
  "loc": "41.2619,-95.8608",
  "org": "AS396982 Google LLC",
  "postal": "51502",
  "timezone": "America/Chicago",
  "readme": "https://ipinfo.io/missingauth"
}%
$
⚠️ Because the app connector is learning routes in realtime, your first request or two might "skip" the app connector. If you don't see the response you expect from the curl ipinfo.io request, repeat it a couple times, and it should quickly change behavior.

You can also use tailscale ping ... to confirm the connector that is being used. This can be helpful when you have multiple connectors deployed for load balancing and/or regional routing and need to confirm or troubleshoot connectivity.

💻 On your laptop, run the command below to see which app connector your laptop is connecting through:

shell

copy
tailscale ping ipinfo.io
You should see output like below.

shell
$ tailscale ping ipinfo.io
pong from workshop (100.88.251.60) via 35.188.4.128:36608 in 264ms
$
Conclusion
You should now have an understanding of:

✅ How to configure a device to advertise as an app connector and how to configure an app
✅ How to configure a device to accept routes to use the app connector
✅ How to confirm connectivity
