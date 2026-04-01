# Initial Account Setup and Client Authentication
Prequisites
If you have not already:

Create a free Tailscale account
Install Tailscale on your laptop
Configure the Tailscale CLI
## Step 1: Remote VM - Confirm Tailscale is installed
Tailscale should already be installed in your workshop virtual machine.

👈 In the terminal for the remote VM on the left run the command below:

```bash
tailscale status
```

You should see output like:

```bash
$ tailscale status
```

Logged out.
## Step 2: Remote VM - Authenticate Tailscale
👈 In the terminal for the remote VM run the command below and then click the link in the output to authenticate the VM to your tailnet:

```bash
tailscale up
```

You should see output like:

```bash
$ tailscale up
```

```bash
To authenticate, visit:

        https://login.tailscale.com/a/...

Success.
```

## Step 3: Authenticate your laptop
If you have not already, open Tailscale and click Log in.... Sign in using the account you used previously to create your account.

💻 On your laptop click the Tailscale menu icon, then click Network Devices, and see that the remote VM is listed as workshop. Click it and Tailscale will copy the Tailscale-specific IP address of workshop to your clipboard.

🎉 You've now confirmed Tailscale is installed and authenticated on both machines.

Conclusion
You should now have an understanding of:

✅ A remote VM authenticated to your tailnet
✅ Your local laptop authenticated to your tailnet
