# Lab 07: DNS and Internet Connectivity Troubleshooting

> **Author:** Nnamso Mkpong
>
> **Domain:** Network Diagnostics — DNS and Name Resolution
>
> **Environment:** Windows 11 Client (Virtualised, domain-joined to mylab.local)
> **Completed:** March 2026

---

## Objective

Separate a DNS name resolution failure from a general internet connectivity loss, prove the difference using command line tools, simulate the fault by setting an unreachable DNS server, and restore correct resolution by pointing the client back to a working DNS server.

---

## Business Scenario

> **Ticket #0055 | Websites Not Loading — DNS or Internet Fault?**
>
> A user reports that websites are not opening in the browser. However, they mention that Microsoft Teams is still connected and receiving messages. IT support has been asked to investigate whether this is a full internet outage or a DNS-specific fault, identify the root cause, and restore normal browsing without restarting the machine or replacing any hardware.

This scenario covers one of the most misunderstood faults in end user support. The symptom looks like the internet is down. The actual cause is that the machine can reach the internet by IP address but cannot translate domain names into addresses, because the DNS server it is pointing to is wrong or unreachable.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Client OS** | Windows 11 |
| **Hostname** | WIN11_CLIENT01 |
| **Client IP** | 192.168.1.49 |
| **Default Gateway** | 192.168.1.1 |
| **Correct DNS** | 8.8.8.8 and 8.8.4.4 (Google Public DNS) |
| **Incorrect DNS used to simulate fault** | 10.10.10.10 and 10.10.10.11 (non-existent) |
| **Tools Used** | Command Prompt, nslookup, ping, Windows Network Settings |
| **Browser** | Microsoft Edge |

---

## Analyst Thinking — Why Websites Fail While Ping Still Works

> **This is the most important concept in this lab and the one that separates a technician who understands networking from one who just follows steps.**

When a user says the internet is down, the first question is: down for everything, or down for browsing?

The browser needs two things to load a website. It needs to translate the domain name into an IP address using DNS, and then it needs a working internet path to reach that IP. If either one fails, the page does not load. But the failure looks the same to the user in both cases.

The test that separates these two scenarios is `ping 8.8.8.8`. This skips DNS completely and sends a packet directly to Google's DNS server by its IP address. If it replies, the internet path is working. If it does not reply, the problem is further upstream.

```
User opens browser → types www.google.com
        │
        ▼
Step 1: DNS lookup
  "What is the IP address for www.google.com?"
        │
        │ DNS server unreachable or wrong
        ▼
  Name resolution fails → browser shows DNS_PROBE_FINISHED_BAD_CONFIG
  The internet itself may be perfectly fine.
        │
        ▼
Step 2: ping 8.8.8.8 (bypasses DNS entirely)
  If this replies → internet is up, DNS is the fault
  If this fails   → internet path is also broken
```

This is why Teams can still work during a DNS fault. Teams connects to Microsoft's servers using cached IP addresses and connection tokens from the previous session. It does not need to perform a fresh DNS lookup every time a message arrives. The browser, on the other hand, performs a DNS lookup every time you navigate to a new address.

---

## Steps Performed

---

### Phase 1 — Confirm Baseline: Everything Working

**Step 1.1 — Verify Websites Load Normally**

Before any changes, confirm that browsing is working. Open the browser and navigate to google.com to establish a clean baseline.

<img width="1019" alt="google.com loading normally at baseline" src="screenshots/01-baseline-google-loading.png" />

> This baseline screenshot proves that the fault introduced later was deliberate and controlled, not pre-existing. Without a before state, there is no proof that anything changed.

---

**Step 1.2 — Ping a Public IP to Confirm Internet Connectivity**

Open Command Prompt and run:

```cmd
ping 8.8.8.8
```

<img width="777" alt="ping 8.8.8.8 returning 4/4 replies at baseline" src="screenshots/02-ping-8.8.8.8-baseline.png" />

> ✅ 4 out of 4 packets returned. The internet path is confirmed working at baseline. Response times of 22–31ms are consistent with a healthy connection to a public server.

---

**Step 1.3 — Run nslookup to Confirm DNS Resolution Works**

Run nslookup against two different domains to confirm that name resolution is functioning before any changes are made:

```cmd
nslookup microsoft.com 8.8.8.8
nslookup google.com 8.8.8.8
```

<img width="879" alt="nslookup microsoft.com and google.com both resolving correctly at baseline" src="screenshots/03-nslookup-baseline-working.png" />

Both domains resolve successfully through 8.8.8.8. The DNS server identifies itself as `dns.google` and returns valid IP addresses for both targets. Baseline DNS is confirmed healthy.

| Domain queried | Result | IP returned |
|---|---|---|
| microsoft.com | Resolved | 13.107.246.56, 13.107.213.56 |
| google.com | Resolved | 142.251.216.46 |

---

### Phase 2 — Introduce the Fault (Wrong DNS Server)

**Step 2.1 — Change DNS to a Non-Existent Server**

Navigate to **Settings → Network and Internet → Ethernet → DNS server assignment → Edit**. Change DNS from Automatic or 8.8.8.8 to manual entries of `10.10.10.10` and `10.10.10.11`. These addresses do not exist on the network and will not respond to any queries.

<img width="698" alt="DNS settings showing incorrect servers 10.10.10.10 and 10.10.10.11" src="screenshots/04-incorrect-dns-settings.png" />

> This simulates the most common real-world DNS fault: a machine that has been manually configured with the wrong DNS server, either by a previous technician setting a static entry incorrectly, or by a DHCP scope pointing to a server that was decommissioned.

---

**Step 2.2 — Attempt to Browse with Wrong DNS**

With the incorrect DNS saved, attempt to open google.com and microsoft.com in the browser.

**google.com:**

<img width="1019" alt="Browser showing DNS_PROBE_FINISHED_BAD_CONFIG on google.com" src="screenshots/05-browser-error-google.png" />

**microsoft.com:**

<img width="1019" alt="Browser showing DNS_PROBE_FINISHED_BAD_CONFIG on microsoft.com" src="screenshots/06-browser-error-microsoft.png" />

> ❌ Both sites fail with the same error: **DNS_PROBE_FINISHED_BAD_CONFIG** and the message that the server IP address could not be found. This error code is Edge's way of reporting a DNS failure specifically, not a general network failure. The browser itself is telling the technician where to look.

The fact that both sites fail with the same error and the same error code confirms this is not a site-specific outage. It is a client-side DNS configuration problem.

---

**Step 2.3 — Ping 8.8.8.8 to Prove Internet Is Still Up**

While the browser is failing on all sites, run:

```cmd
ping 8.8.8.8
```

<img width="706" alt="ping 8.8.8.8 returning 4/4 replies even while browser cannot load pages" src="screenshots/07-ping-still-works-during-dns-fault.png" />

> ✅ 4 out of 4 packets returned. The internet path is completely intact. The machine is sending and receiving data to and from the internet without any issues. Only domain name resolution has failed.

This is the key diagnostic moment. The internet is up. DNS is broken. These are two separate things.

---

**Step 2.4 — Run nslookup to Confirm DNS Resolution Has Failed**

```cmd
nslookup microsoft.com
nslookup google.com
```

<img width="1010" alt="nslookup microsoft.com and google.com both timing out with wrong DNS server" src="screenshots/08-nslookup-failed-wrong-dns.png" />

> ❌ Both queries return: **DNS request timed out. timeout was 2 seconds.** The server shown is `10.10.10.10` which is listed as `Unknown` because it does not exist. No IP addresses are returned for either domain. Name resolution is completely broken.

This output confirms the root cause. The DNS server the machine is querying does not exist. Every domain lookup fails. The internet path works but without name resolution, the browser cannot function.

---

### Phase 3 — Flush the DNS Cache

**Step 3.1 — Run ipconfig /flushdns**

Before restoring the DNS settings, flush the local DNS cache to clear any stale or incorrect entries that may have been stored from previous lookups:

```cmd
ipconfig /flushdns
```

Expected output:

```
Windows IP Configuration

Successfully flushed the DNS Resolver Cache.
```

> Flushing the cache forces the machine to perform fresh DNS lookups rather than returning cached results. In a real fault this step matters because a machine may have a cached incorrect result stored from before the fault was introduced. Restoring the DNS server without flushing the cache could mean the machine continues returning stale results for a short period.

---

### Phase 4 — Restore the Correct DNS Server

**Step 4.1 — Set DNS Back to 8.8.8.8 and 8.8.4.4**

Return to **Settings → Network and Internet → Ethernet → DNS server assignment → Edit**. Set Preferred DNS to `8.8.8.8` and Alternate DNS to `8.8.4.4`. Click Save.

<img width="691" alt="DNS settings restored to 8.8.8.8 and 8.8.4.4" src="screenshots/09-dns-restored-correct-settings.png" />

> In a domain environment the correct DNS server would typically be the Domain Controller IP, not a public resolver. In this lab the machine is not relying on AD name resolution for external browsing, so Google Public DNS is the correct choice. On a domain-joined machine you would restore the DC IP and verify both internal and external resolution.

---

**Step 4.2 — Run nslookup Again to Confirm Resolution Is Restored**

```cmd
nslookup microsoft.com
nslookup google.com
```

<img width="763" alt="nslookup microsoft.com and google.com both resolving correctly after restore" src="screenshots/10-nslookup-restored-working.png" />

> ✅ Both domains resolve immediately. The server is identified as `dns.google` at `8.8.8.8`. IP addresses are returned for both targets. DNS is fully restored.

---

**Step 4.3 — Confirm Websites Load in the Browser**

Open microsoft.com in the browser to confirm end-to-end browsing has been restored.

<img width="1013" alt="microsoft.com loading fully after DNS restored" src="screenshots/11-microsoft-loading-restored.png" />

> ✅ microsoft.com loads fully. The DNS lookup succeeded, the browser retrieved the correct IP address, and the page content loaded. The fault is resolved.

---

## Help Desk Ticket Note

See `TICKET-0055-dns-troubleshooting.md` in this folder.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Baseline ping 8.8.8.8 confirmed internet working | Pass |
| Baseline nslookup confirmed DNS resolving for microsoft.com and google.com | Pass |
| Incorrect DNS server set to 10.10.10.10 and 10.10.10.11 | Pass |
| Browser returns DNS_PROBE_FINISHED_BAD_CONFIG on all sites | Pass |
| ping 8.8.8.8 still returns 4/4 replies during DNS fault | Pass |
| nslookup returns DNS request timed out with wrong server | Pass |
| ipconfig /flushdns cleared the resolver cache | Pass |
| DNS restored to 8.8.8.8 and 8.8.4.4 | Pass |
| nslookup resolves microsoft.com and google.com after restore | Pass |
| Browser loads microsoft.com fully after restore | Pass |

---

## What I Learned

1. **DNS failure and internet failure look identical to the user.** Both produce the same result in the browser: the page does not load. The technician's job is to run one test, `ping 8.8.8.8`, to immediately separate the two. If that ping works, the internet is up and DNS is the fault. If it fails, the problem is deeper.

2. **DNS_PROBE_FINISHED_BAD_CONFIG is a specific error code.** When Edge or Chrome returns this code it is explicitly reporting a DNS configuration problem, not a general network failure. Reading browser error codes accurately removes guesswork from the diagnosis.

3. **Some applications cache their connections and survive a DNS fault.** Teams, Outlook, and other always-on applications often maintain persistent connections that were established before the DNS fault occurred. They appear to be working while the browser fails completely. This is why the classic symptom is websites down but Teams still connected, and it is not a contradiction.

4. **flushdns is part of the restore process, not just a random step.** Without flushing, the machine might return cached results for a period after the DNS server is corrected. Running ipconfig /flushdns ensures that all subsequent lookups go to the newly configured server and return fresh results.

5. **nslookup is more specific than ping for DNS problems.** Ping to a domain name does involve DNS, but nslookup gives you the full resolver output: which server was queried, what was returned, and whether the response was authoritative. When DNS is failing, nslookup tells you exactly where and why in a way that a failed ping alone cannot.

---

## Real World Relevance

DNS faults are among the most misdiagnosed connectivity complaints on a helpdesk. Users describe the symptom as the internet is down, and a technician who does not know the difference between DNS resolution and internet connectivity will either raise a network team ticket unnecessarily or spend time rebooting routers that have nothing wrong with them.

The ability to run ping to a public IP and immediately rule the internet path in or out is a skill that saves time on every single network call. Once the internet path is confirmed working, the diagnostic moves immediately to DNS: check which server the machine is using, try nslookup, and if it times out, the answer is in the DNS configuration, not anywhere else on the network.

In real environments this fault appears most often after a DHCP scope is updated with an incorrect DNS entry, after a DNS server is decommissioned without updating all clients, after a technician manually sets a static DNS entry and types it incorrectly, or after a user changes their own network settings. In every case the fix is the same: identify the correct DNS server, update the setting, flush the cache, and verify. The entire process takes under five minutes once the technician knows the correct diagnostic sequence.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| Browser shows DNS_PROBE_FINISHED_BAD_CONFIG | DNS server unreachable or set incorrectly | Check DNS in adapter settings; set to correct server; flush cache |
| nslookup returns DNS request timed out | DNS server does not exist or is not responding | Change DNS to 8.8.8.8 or domain controller IP; retry nslookup |
| ping 8.8.8.8 works but websites do not load | Pure DNS fault, internet path is fine | Fix DNS settings and flush cache |
| ping 8.8.8.8 fails and websites do not load | Internet path is also broken, not just DNS | Move to network adapter and gateway diagnostics |
| Teams works but browser fails | Teams using cached connection, DNS broken for new lookups | DNS fault; fix DNS settings |
| flushdns succeeds but problem persists | DNS server still wrong after flush | Verify the DNS server setting was saved correctly; retry |
| nslookup works but browser still fails | Browser using stale cached result | Clear browser cache or try incognito mode; wait for TTL to expire |
