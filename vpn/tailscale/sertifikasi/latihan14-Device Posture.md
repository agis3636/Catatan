Device Posture
Introduction
Device posture is a mechanism to measure how secure or trustworthy a device is. With Tailscale you can collect device attributes and use them as part of connectivity rules within your Tailscale network (known as a tailnet). You can use these rules to limit access for devices in your tailnet that do not meet security requirements.

Step 1: Define postures
Before enforcing device posture for access rules we first need to define the posture and set of assertions or conditions to apply to devices. Tailscale has built-in posture attributes for operating system (OS), OS version, Tailscale version, and more. For this exercise we'll use the built-in OS attributes.

macOS posture
Open the admin console then click the Access controls panel if not already open.
Click the Visual editor toggle then click Device posture then click + Add device posture
Enter macos as the Name, then click ⊕ Posture assertion, click node:os, then in the Enter node:os dropdown select macos.
Click Save device posture.
Windows posture
Repeat the steps for Windows.

Open the admin console then click the Access controls panel if not already open.
Click the Visual editor toggle then click Device posture then click + Add device posture
Enter windows as the Name, then click ⊕ Posture assertion, click node:os, then in the Enter node:os dropdown select windows.
Click Save device posture.
Step 2: Add source posture to grants
Open the admin console then click the Access controls panel if not already open.
Click the Visual editor toggle then click General access rules.
Find the access rule with tag:connector as the destination.
Click the ... menu then click Edit.
Change Source to autogroup:member, then expand > Advanced options in Device posture, select both posture:macos and posture:windows.
Click Save grant.
Confirm connectivity
💻 On your laptop, run the command below to test connectivity to the device:

shell

copy
curl IP
You should see output like:

shell
$ curl 100.88.251.60
Succesful response from NGINX.
$
You should still see be able to curl to the remote VM, because while we implemented device posture, your device should be passing the added posture conditions.

Break your access
If you're on a macOS machine

Return to the admin console.
Remove posture:macos from Device posture.
Click Save grant.
This enforces access can only come from Windows machines.

Alternatively, if you're on a Windows machine:

Return to the admin console.
Remove posture:windows from Device posture.
Click Save grant.
Test connectivity
💻 On your laptop, run the command below to test connectivity to the device:

shell

copy
curl --connect-timeout 5 workshop
You should see output like:

shell
$ curl workshop
curl: (28) Failed to connect to workshop port 80 after 5003 ms: Timeout was reached
$
Your curl should timeout this time, because your device is no longer passing the posture conditions.

Revert
Return to the admin console.
Add posture:macos or posture:windows from Device posture.
Click Save grant.
Step 3: Default source posture
In the previous steps we implemented posture for an individual access rule. You can also apply a baseline posture that applies to all of your access rules.

Open the admin console then click the Access controls panel if not already open
Click the JSON editor toggle to see the policy file in JSON form.
Add the "defaultSrcPosture" section below and click Save.
json

copy
"defaultSrcPosture": [
  "posture:macos",
  "posture:windows",
],
This default source posture requires devices to be macOS or Windows to connect to any other device on the tailnet.

Endpoint detection and response (EDR) and mobile device management (MDM) integrations
The Tailscale controlplane can synchronize what your EDR and MDM tools know about your devices (by matching on device serial numbers) allowing you to incorporate MDM/EDR device attributes into your access rules as well. Each EDR and MDM exposes different attributes. For example, integrating Crowdstrike Falcon will provide a falcon:ztaScore attribute on matched devices. While integrating InTune will provide a intune:complianceState attribute and others.

Integrating your EDR and MDM tools and using their data for device posture gets you one step closer to Zero Trust.

Conclusion
You should now have an understanding of:

✅ How to define device postures in your policy file
✅ How to define access rules based on device posture
✅ How EDR and MDM tools can be integrated
