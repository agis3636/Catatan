<img width="1297" height="893" alt="image" src="https://github.com/user-attachments/assets/80f2f3d4-fd9c-48c8-a344-648469e874ca" /># Client Configuration

Introduction
There are many settings that can be configured in the Tailscale client. These settings can be configured with the tailscale CLI or in the GUI client for macOS and Windows.

## Step 1: Device Name
👈 In the terminal for the remote VM, run the command below to change the Tailscale device name.

```bash
run
tailscale set --hostname remote-workstation
You should see output like:

shell
$ tailscale set --hostname remote-workstation
$
```

👈 In the terminal for the remote VM, run tailscale status to see the change.

```bash
run
tailscale status
You should see output like:

shell
$ tailscale status
100.111.174.16  remote-workstation            cameron@  linux  -
100.120.125.20  cameron-macbookair-tailscale  cameron@  macOS  -
$
```

Open the admin console to see the change represented there too.

## Step 2: Tailscale SSH
👈 In the terminal for the remote VM, run the command below to change the Tailscale device name.

```bash
run
tailscale set --ssh
You should see output like:

shell
$ tailscale set --ssh
$
```

We will test Tailscale SSH in a later exercise.

## Step 3: Additional settings
💻 In your laptop terminal, run the commands below to configure additional settings.

```bash
run
tailscale set --accept-dns
You should see output like:

shell
$ tailscale set --accept-dns
$
```

Advanced Topics
Mobile Device Management (MDM)
Mobile Device Management (MDM) tools such as Jamf, Iru (formerly Kandji), InTune, and more can be used to deploy and configure Tailscale.

MDM tools can also be used to pre-configure client settings, such as --accept-dns, --accept-routes, etc. so that the Tailscale client software works as expected on employee machines. Most settings can also be hidden so that employess cannot change them.

Conclusion
You should now have an understanding of:

✅ How to configure various client settings
✅ How MDM can help in enterprise environments
