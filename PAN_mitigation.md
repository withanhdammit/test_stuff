On 2025-01-13 around 0309, <strong>redacted</strong> became the target of a brute-force cyber attack.  Logs show well over 15 million login attempts were made at the Headquarters facility between 0309 and 1251.  None of the login attempts were successful, however the threat actor was using a list of known usernames.  It is unknown where the list came from.

As a result of using known usernames in a brute-force attack, numerous production login accounts were repeatedly disabled due to the existing password lockout policies enforced on the internal.company.com Active Directory domain, resulting in legitimate employees unable to log in to their account and were not able to perform their assigned duties.

This is similar to an attack that occurred on 2024-12-30.  After that attack a number of provisions were added to protect the environment from a similar attack that proved to not be as effective as indicated.

These are documented in Emergency Change 181, details can be viewed at https://servicedesk.company.com/a/changes/181.  The articles followed were entitled How to Protect GlobalProtect Portal on NGFW from Brute Force Attack https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA14u0000010zEJCAY and Detecting Brute Force Attack on GlobalProtect Portal Page https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000ClJ2CAK.

During this brute-force attack additional research was performed and a community article was located at https://live.paloaltonetworks.com/t5/globalprotect-discussions/use-auto-tagging-to-block-failed-global-protect-login-attempts/m-p/1085882, describing similar details of the attack currently underway against the <strong>redacted</strong> network.

There are a number of provisions included in the article, most of which were already enabled on the <strong>redacted</strong> firewalls.

- Block all traffic to your GP public IP address except the source country or countries you need.  This stopped a significant amount of failed attempts.
  - Some source countries are already blocked, however since business is performed across the world, all countries cannot be unilaterally banned.
  - It may be worth configuring the inbound GlobalProtect rules to only allow connections from the United States similar to the ITAR GlobalProtect rule.  Then to create a second firewall access rule allowing connections from other countries, but only if the person is a member of an 'International VPN Access' group.  This could align well with the policy requiring all personnel to report international travel.  If they do, their login account could be added to the international access group, which would allow them to access the VPN when in a foreign country, and subsequently removed from the group at the cessation of the international travel time period.
- Always block the built-in PAN-OS EDLs inbound (and outbound), and any additional ones you prefer.
  - These External Dynamic Lists are already enabled for both inbound and outbound traffic.
- Allow only 'panos-global-protect' and 'ipsec-esp-udp' applications to connect to your GP public IP address with service application-default.
  - The above Palo Alto Knowledge Base articles indicate to also allow 'ssl' and 'web-browsing', however removing these two from the inbound rule made a significant drop in the attack attempts.  These application profiles are required for GlobalProtect to work, however they are secondary protocols and are inherent in the 'panos-global-protect' application profile, which means 'panos-global-protect' must be indicated for 'ssl' or 'web-browsing' to be allowed.
- Disable the GP portal page.  It is not needed for GP to work and GP can even upgrade the client with the portal page disabled.  There are multiple workarounds for new client software deployment that can be discussed later.
  - This was disabled previously after a recommendation during an external penetration test.
- If you want to stop all brute force attempts, consider using a whitelist of IP addresses.  If you have a small user base or all of your users live in one geographical region, developing an EDL for the ISP CIDR blocks is easier than you would think.  This step would also do a great deal to block Day 0 attacks.  All of the hacking attempts I have seen use hosting provider IP addresses and not residential/business IP addresses.
  - This is not feasible for the <strong>redacted</strong> Environment

The main solution in the article, is to enable a Vulnerability Protection Profile to watch the number of successive connection attempts and mark repeated attempts in a short period (this was configured to be 10 attempts in 10 minutes) as a threat and to deny access to the source IP addresses from these attempts.

Once these provisions were put into place the failed login attempts completely stopped.

These changes were done to the firewalls at all three facilities that host inbound GlobalProtect VPN portals.
