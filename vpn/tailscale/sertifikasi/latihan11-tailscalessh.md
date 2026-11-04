Tailscale SSH
Introduction
Tailscale SSH is part of Tailscale's portfolio of privileged access management (PAM) products to control, secure, and audit access for SSH, Kubernetes, and databases.

Tailscale SSH handles the following PAM concerns:

Credential injection - you do not need to manage, distribute, revoke, etc. SSH keys. Tailscale SSH handles this for you automatically.
Session recording - optionally require session recording. Sessions are recorded to your cloud bucket or disk in your infrastructure.
Privilege escalation - optionally require certain connections, or connections as certain users (for example, root), to re-authenticate before connecting.
Step 1: Enable Tailscale SSH on the remote VM
👈 In the terminal for the remote VM, run the command below to change the Tailscale device name.

shell

copy

run
tailscale set --ssh
You should see output like:

shell
$ tailscale set --ssh
$
Step 2: Configure Tailscale SSH in the policy file
The default Tailscale policy file comes pre-configured with common SSH rules, but we've added some tags and made some changes so let's replace the existing rule with a new one

Open the admin console then click the Access controls panel if not already open.
Click the JSON editor toggle.
Find the "ssh" section in the JSON.
Copy the JSON below and past over the the entire "ssh" section, replacing it completely.
Scroll to the bottom of the page and click Save.
json

copy
"ssh": [
  {
    "action": "check",
    "src":    ["autogroup:member"],
    "dst":    ["autogroup:self", "tag:connector"],
    "users":  ["autogroup:nonroot", "root"],
  },
],
Step 3: Test Tailscale SSH
💻 On your laptop, run the command below to use Tailscale SSH to connect to the remote VM. Type yes and hit ENTER when prompted to continue connecting.

shell

copy
ssh root@workshop
You should see output like below.

shell
$ ssh root@workshop
The authenticity of host 'workshop (100.88.251.60)' can't be established.
ED25519 key fingerprint is: SHA256:+9VpMeM6iqdg3Jp24sIpG/09iUKVC80lR40CCbE2rOw
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'workshop' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.1 LTS (GNU/Linux 6.8.0-1015-gcp x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sun Mar 29 15:14:29 UTC 2026

  System load:  0.0               Processes:             113
  Usage of /:   26.7% of 8.65GB   Users logged in:       0
  Memory usage: 6%                IPv4 address for ens4: 10.32.0.29
  Swap usage:   0%

  => There are 11 zombie processes.

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

264 updates can be applied immediately.
140 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


$
🎉 You've now successfully configured and used Tailscale SSH to connect to the remote VM without having to create and manage SSH keys.

Conclusion
You should now have an understanding of:

✅ How to configure Tailscale SSH
✅ How to use Tailscale SSH
