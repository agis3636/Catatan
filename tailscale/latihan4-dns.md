Tailscale DNS
Introduction
You can manage DNS settings for your tailnet including settings for MagicDNS, nameservers, and search domains.

Step 1: Configure global nameservers
A global nameserver handles DNS queries for any domain. You can use a public DNS nameserver or run your own that to include additional DNS mappings. When you use a public global nameserver, such as Cloudflare or Google, Tailscale automatically uses DNS-over-HTTPS (DoH) to ensure your DNS queries are encrypted before traversing the internet.

Open the admin console then open the DNS page if not already open.
Scroll down to Nameservers.
Click the Add nameserver button then click Google Public DNS.
Toggle on Override DNS servers.
Step 2: Configure clients to use Tailscale DNS
Tailscale clients must be configured to accept tailnet DNS settings. As we learned in the client configuration exercise, you can configure client settings in the GUI and CLI.

Configuration via GUI
💻 On your laptop in the GUI settings, enable Use Tailscale DNS settings.

Configuration via CLI
💻 In your laptop terminal, run the command to accept DNS settings.

shell

copy
tailscale set --accept-dns
You should see output like:

shell
$ tailscale set --accept-dns
$
Step 3: Confirm DNS settings
Global nameserver settings can be hard to validate. The tailscale CLI provides a way to confirm local DNS settings.

💻 In your laptop terminal, run the command to confirm DNS settings.

shell

copy
tailscale dns status
You should see output like:

shell
$ tailscale dns status

=== 'Use Tailscale DNS' status ===

Tailscale DNS: enabled.

Tailscale is configured to handle DNS queries on this device.
Run 'tailscale set --accept-dns=false' to revert to your system default DNS resolver.

=== MagicDNS configuration ===

This is the DNS configuration provided by the coordination server to this device.

MagicDNS: enabled tailnet-wide (suffix = tail6c02e5.ts.net)

Other devices in your tailnet can reach this device at cameron-macbookair-tailscale.tail6c02e5.ts.net.

Resolvers (in preference order):
  - 8.8.8.8
  - 8.8.4.4
  - 2001:4860:4860::8888
  - 2001:4860:4860::8844

Split DNS Routes:
  - ipinfo.io                      -> http://100.88.251.60:58091/dns-query
  - ts.net.                        -> 199.247.155.53
  - ts.net.                        -> 2620:111:8007::53

Search Domains:
  - tail6c02e5.ts.net

=== System DNS configuration ===

This is the DNS configuration that Tailscale believes your operating system is using.
Tailscale may use this configuration if 'Override Local DNS' is disabled in the admin console,
or if no resolvers are provided by the coordination server.

Nameservers:
  - 10.7.0.1
  - 111.68.124.150
  - 203.99.131.10
  - 175.176.164.174
  - 103.127.169.254

Search domains:
  (no search domains found)

[this is a preliminary version of this command; the output format may change in the future]
$
Step 4: Confirm DNS with a query
The tailscale CLI also provides a way to confirm local DNS settings with a DNS query.

💻 In your laptop terminal, run the command to confirm DNS settings with a DNS query.

shell

copy
tailscale dns query tailscale.com
You should see output like:

shell
$ tailscale dns query tailscale.com
DNS query for "tailscale.com" (A) using internal resolver:

Multiple resolvers available:
  - https://dns.google/dns-query
  - 8.8.8.8
  - 8.8.4.4
  - 2001:4860:4860::8888
  - 2001:4860:4860::8844

Response code: RCodeSuccess

Name            TTL  Class      Type   Body
----            ---  -----      ----   ----
tailscale.com.  24   ClassINET  TypeA  76.76.21.21

$
Restricted nameservers or split DNS
A restricted nameserver only applies to DNS queries matching a specific search domain. Using a restricted nameserver is also known as split DNS. An example of a split DNS scenario would be if you configure 1.1.1.1 as a nameserver for the domain example.com. This tells devices in your tailnet to only use the 1.1.1.1 name server to look up DNS queries that match *.example.com (such as foo.example.com and bar.example.com).

Restricted nameservers can be especially helpful for companies that already have their own internal DNS or for companies that want to use a cloud provider's DNS resolvers.

Conclusion
You should now have an understanding of:

✅ How to configure tailnet-wide DNS settings
✅ How to configure clients to use tailnet-wide DNS settings
