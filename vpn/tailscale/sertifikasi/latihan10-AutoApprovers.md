Auto Approvers
Introduction
Earlier in the workshop we talked about Auto Approvers briefly. We'll now return to them and explain them in more detail.

As a precaution against unexpected and potentially malicious activity, the Tailscale controlplane does not allow any person or device to start advertising a device as an exit node, subnet router, or app connector without being approved. Tailscale supports approving routes manually via the admin console, via the API, or via Auto Approvers. Approving routes manually can work at a small scale, but most companies do not like manual process. So they can opt to use the API or use Auto Approvers. Most companies choose Auto Approvers.

The autoApprovers section of the tailnet policy file defines the list of tags and routes that will be automatically approved. This is especially helpful for large companies and for companies that programmatically provision and manage infrastructure with Infrasture-as-Code so that the complete process is automated.

Example
For the setup part of the Connectors exercise we added the autoApprovers section below. The rules here define that any Tailscale administrator (as defined by autogroup:admin) and any device tagged with tag:connector will automatically approve routes for ::/ and 0.0.0.0/0 (for Exit Nodes and App Connectors) and 10.0.0.0/8 (for Subnet Routers).

json

copy
"autoApprovers": {
  "routes": {
    "0.0.0.0/0":  ["autogroup:admin", "tag:connector"],
    "10.0.0.0/8": ["autogroup:admin", "tag:connector"],
  },
},
Conclusion
You should now have an understanding of:

✅ How and when to use auto approvers
