User & Device Approval
Introduction
By default the Tailscale controlplane allows any authorized and authenticated user or device to join the tailnet.

When user approval is enabled, the first time a new user logs in to a tailnet, the user's status is set to pending. While in a pending state, the user can connect their device to the Tailscale controlplane, but cannot connect to other devices in the tailnet. An Owner, Admin, or IT admin of the tailnet can review the user information and set the user status to approved, or remove the user.

Similarly, when device approval is enabled, a new device that accesses your network displays a notification that the device is awaiting approval. A device awaiting approval cannot send or receive traffic on your Tailscale network until it is approved.

Step 1: Enable user approval
Open the admin console then click the Settings panel if not already open.
Click User Management on the left and scroll down to User Approval.
Enable Manually approve new users.
Step 2: Enable device approval
Open the admin console then click the Settings panel if not already open.
Click Device Management on the left and scroll down to User Approval.
Enable Manually approve new devices.
Step 3: Customize key expiry
Open the admin console then click the Settings panel if not already open.
Click Device Management on the left and scroll down to Key Expiry.
Change the days value to 30 to require users to re-authenticate more frequently.
ℹ️ Key expiry is disabled by default for tagged devices. The setting only applies to user devices.

Step 4: Confirm settings
Because this workshop is in a sandbox environment it can be difficult to test these settings without adding additional users or devices to your tailnet. If you have an additional device with you (e.g. your phone or tablet), you can install Tailscale on it and then authenticate it to your tailnet. The device will show pending and then you, as the tailnet administrator, will have to approve the device in the admin consle.

Conclusion
You should now have an understanding of:

✅ How user approval works
✅ How device approval works
