# Email Deliverability - Trainee Guide

## Introduction

Welcome to email authentication! This guide will help you understand how to ensure emails from your domains reach their intended recipients and don't get marked as spam or blocked. You'll learn about three core technologies: SPF, DKIM, and DMARC.

**Why this matters:**
- Prevents your legitimate emails from being marked as spam
- Protects your domain from being used for phishing attacks
- Major email providers (Google, Yahoo, Microsoft) now require these for bulk sending
- Builds trust and improves email deliverability

---

## The Three Technologies: Quick Overview

### SPF (Sender Policy Framework)
**What it does**: Lists which servers are allowed to send email for your domain.

**Think of it as**: A guest list at a door. If the server isn't on the list, it's not allowed in.

**Example**: "Only these IP addresses can send email for example.com"

### DKIM (DomainKeys Identified Mail)
**What it does**: Adds a digital signature to your emails to prove they're authentic and haven't been tampered with.

**Think of it as**: A wax seal on a letter. If the seal is broken or fake, you know something's wrong.

**Example**: "This email was really sent by example.com and hasn't been modified"

### DMARC (Domain-based Message Authentication, Reporting & Conformance)
**What it does**: Tells other email servers what to do if an email fails SPF or DKIM checks, and sends you reports about who's sending email using your domain.

**Think of it as**: The security policy and monitoring system that makes sure the guest list (SPF) and wax seals (DKIM) are working properly.

**Example**: "If an email fails our checks, put it in spam" + "Send me daily reports"

---

## SPF (Sender Policy Framework)

### What You Need to Know

SPF is a DNS record that lists which IP addresses and servers are allowed to send email for your domain. When someone receives an email claiming to be from your domain, their email server checks your SPF record to see if the sending server is authorized.

### How to Create an SPF Record

SPF records are published as TXT records in your DNS. They always start with `v=spf1` and end with an "all" mechanism.

**Basic SPF record structure:**
```
v=spf1 [authorized servers] [all qualifier]
```

### Common Mechanisms

**IP addresses** - Explicitly authorize specific IPs:
```
v=spf1 ip4:192.0.2.1 ip4:198.51.100.0/24 -all
```

**Include** - Authorize third-party services:
```
v=spf1 include:_spf.google.com include:sendgrid.net -all
```

**A record** - Authorize your domain's main server:
```
v=spf1 a -all
```

**MX records** - Authorize your mail exchange servers:
```
v=spf1 mx -all
```

**Combined example** (typical):
```
v=spf1 ip4:192.0.2.1 include:_spf.google.com include:sendgrid.net -all
```

### The "all" Qualifier

The ending of your SPF record tells servers what to do with emails from unauthorized sources:

- **-all** (fail): Reject emails from unauthorized servers - STRICT
- **~all** (softfail): Accept but mark as suspicious - RECOMMENDED while testing
- **?all** (neutral): No policy - NOT recommended

**Best practice**: Start with `~all` while testing, move to `-all` once you're confident.

### The 10 Lookup Limit - IMPORTANT!

**This is the #1 SPF problem people run into.**

SPF has a limit of 10 DNS lookups. Each `include`, `a`, and `mx` mechanism counts as a lookup. If you exceed 10 lookups, SPF breaks completely (returns "permerror").

**Example that exceeds the limit:**
```
v=spf1 include:service1.com include:service2.com include:service3.com 
       include:service4.com include:service5.com ... -all
```
If each service has nested includes, you'll quickly exceed 10 total lookups.

**How to avoid this:**
1. **Use explicit IP addresses** when possible (don't count against limit)
2. **Use subdomains** for different email types (each gets its own 10-lookup budget)
3. **Only include services you actually use**

**Example with subdomains:**
```
# Main domain
example.com → v=spf1 a -all

# Marketing subdomain  
marketing.example.com → v=spf1 include:salesforce.com -all

# Support subdomain
support.example.com → v=spf1 include:zendesk.com -all
```

### Common SPF Mistakes

1. **Forgetting a service** - Email from that service will fail SPF
2. **Exceeding 10 lookups** - SPF stops working entirely
3. **Using `-all` too soon** - Blocks legitimate email you forgot about
4. **Never updating** - Third-party services change IPs, your record gets stale

### Testing Your SPF Record

Before publishing:
1. Use an SPF checker tool (MxToolbox, EasyDMARC)
2. Verify lookup count is under 10
3. Check for syntax errors
4. Send test emails and check headers

---

## DKIM (DomainKeys Identified Mail)

### What You Need to Know

DKIM uses cryptography to sign your emails. When you send an email, your server adds a digital signature. When someone receives it, their server uses a public key (published in your DNS) to verify the signature is valid.

**Two keys:**
- **Private key**: Kept secret on your mail server (signs emails)
- **Public key**: Published in DNS (verifies signatures)

### How DKIM Works - The Process

1. **Your server** creates a hash of the email content
2. **Your server** encrypts that hash with your private key
3. **Your server** adds a DKIM-Signature header to the email
4. **Receiving server** retrieves your public key from DNS
5. **Receiving server** decrypts the signature and compares hashes
6. **If they match** → Email is authentic and unchanged ✓

### DKIM DNS Records

DKIM public keys are published at a specific location in DNS:
```
[selector]._domainkey.[yourdomain.com]
```

**Example:**
```
s2026q1._domainkey.example.com
```

**The DNS record contains:**
```
v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDXYfp...
```

- `v=DKIM1` = DKIM version
- `k=rsa` = Key type (RSA encryption)
- `p=` = The actual public key (long base64 string)

### Selectors

The "selector" is a name you choose to identify which DKIM key you're using. Why use selectors?

**Allows multiple keys:**
- `s2026q1._domainkey.example.com` (current key)
- `s2026q2._domainkey.example.com` (new key)
- `s2025q4._domainkey.example.com` (old key being phased out)

**Benefits:**
- Rotate keys without breaking existing emails
- Different systems can use different keys
- Test new keys before removing old ones

### DKIM Canonicalization - Important Setting

Canonicalization determines how strictly DKIM checks the email format. Two options:

**Simple**: Requires exact match
- If any formatting changes (even a space), signature fails
- Very strict, often breaks

**Relaxed**: Allows minor formatting differences
- Ignores whitespace changes
- Handles emails passing through multiple servers
- More reliable

**Best practice**: Use **relaxed/relaxed** (relaxed for both headers and body)

### Setting Up DKIM

**Typical process:**
1. Generate a key pair (your hosting provider usually does this)
2. Publish public key in DNS at `selector._domainkey.yourdomain.com`
3. Configure your mail server to sign emails with the private key
4. Test by sending an email and checking headers for DKIM-Signature

**For web hosting customers:**
- Your hosting provider usually sets this up automatically
- They might use CNAME delegation (you create a CNAME, they manage the key)

### DKIM Key Rotation

**Security best practice**: Change your DKIM keys every 6 months.

**Why rotate?**
- Limits damage if a key is compromised
- Prevents key expiration issues
- Good security hygiene

**How to rotate without breaking email:**
1. Generate new key pair
2. Publish new public key with new selector
3. Wait for DNS to propagate (24-48 hours)
4. Configure servers to use new selector
5. After all servers migrated, remove old selector

### Common DKIM Mistakes

1. **Typo in DNS record** - Signature verification fails
2. **Wrong selector** - Server looks for wrong DNS record
3. **Using simple/simple canonicalization** - Signatures break when email reformatted
4. **Never rotating keys** - Security risk
5. **Not publishing DNS record** - Can't verify signatures

### Testing DKIM

1. Send a test email
2. Check email headers for "DKIM-Signature" header
3. Use DKIM validator tool (MxToolbox, EasyDMARC)
4. Verify the DNS record exists and is correct

---

## DMARC (Domain-based Message Authentication, Reporting & Conformance)

### What You Need to Know

DMARC is the "policy layer" that:
1. Checks if SPF and DKIM pass
2. Requires "alignment" (explained below)
3. Tells email servers what to do with failures
4. Sends you reports about who's sending email using your domain

**Think of DMARC as the manager** that makes sure SPF and DKIM are working together properly.

### Alignment - The Critical Concept

**Alignment** means the domain in the visible "From" address matches the domain authenticated by SPF or DKIM.

**Example:**
```
Visible From: user@example.com
SPF authenticates: example.com ✓ (aligned)
DKIM authenticates: example.com ✓ (aligned)
```

**Why alignment matters:**
Without it, attackers could send from an authorized server but fake the "From" address:
```
Visible From: ceo@yourcompany.com (what recipient sees)
SPF authenticates: attacker.com (the actual sender)
```
SPF passes, but it's not aligned with what the recipient sees → DMARC catches this!

**Alignment types:**
- **Relaxed** (recommended): Organizational domain must match
  - `user@example.com` aligns with DKIM `d=mail.example.com` ✓
- **Strict**: Exact domain match required
  - `user@example.com` aligns with DKIM `d=mail.example.com` ✗

**Use relaxed alignment** - strict provides no extra security and complicates setup.

### DMARC DNS Record

DMARC records are published at:
```
_dmarc.[yourdomain.com]
```

**Basic DMARC record:**
```
v=DMARC1; p=none; rua=mailto:dmarc-reports@example.com;
```

**Tags explained:**
- `v=DMARC1` = Version (required, must be first)
- `p=` = Policy (what to do with failures)
- `rua=` = Where to send aggregate reports
- `pct=` = Percentage of messages to apply policy to (optional)
- `sp=` = Policy for subdomains (optional)

### The Three DMARC Policies

**p=none** (Monitoring Mode):
- Do nothing with failing emails (deliver normally)
- Just send me reports
- Use this when starting DMARC
- **Provides ZERO protection** - visibility only

**p=quarantine** (Intermediate):
- Put failing emails in spam folder
- Don't delete them (recipient can still see them)
- Good middle step between none and reject
- Some protection, low risk

**p=reject** (Maximum Protection):
- Reject failing emails completely
- They won't be delivered at all
- **This is your ultimate goal**
- Only use when you're confident everything is configured correctly

### The DMARC Implementation Journey

**Don't skip straight to p=reject!** Follow this path:

**Phase 1: Start with p=none (Monitoring)**
```
v=DMARC1; p=none; rua=mailto:dmarc-reports@example.com;
```
- Duration: Several weeks to months
- Monitor reports to identify all email sources
- Ensure SPF and DKIM configured for each source
- Look for any failures

**Phase 2: Move to p=quarantine**
```
v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@example.com;
```
- Duration: Weeks to months
- Watch for legitimate emails going to spam
- Fix any configuration issues
- Build confidence

**Phase 3: Reach p=reject**
```
v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com;
```
- Only when reports show everything aligned
- Maximum protection achieved
- Continue monitoring reports

### DMARC Reports

**Aggregate Reports (rua)**:
- Daily reports (XML format)
- Show statistics about your email
- Which IPs are sending for your domain
- Which emails passed/failed authentication
- Essential for monitoring

**What to do with reports:**
1. Use a DMARC report analyzer tool (they convert XML to readable dashboards)
2. Identify all sources sending email for your domain
3. Verify each source is properly configured (SPF + DKIM)
4. Look for abuse attempts (unauthorized sending)
5. Monitor trends over time

### Subdomain Protection - Don't Forget!

**Common mistake**: Protecting main domain but forgetting subdomains.

**Example vulnerability:**
```
example.com → p=reject (protected)
mail.example.com → no DMARC or p=none (VULNERABLE)
```

Attackers can send from `support@mail.example.com` and bypass your protection!

**Fix options:**
1. Set separate DMARC policy for each subdomain
2. Use `sp=` tag to set policy for all subdomains:
```
v=DMARC1; p=reject; sp=reject; rua=mailto:dmarc-reports@example.com;
```

### Common DMARC Mistakes

1. **Staying at p=none forever** - No protection!
2. **Skipping to p=reject too fast** - Blocks legitimate email
3. **Not reading reports** - Miss configuration issues
4. **Forgetting subdomains** - Leaves vulnerability
5. **Using pct=50 long-term** - Only protects 50% of email
6. **Wrong tag order** - Must start with v=DMARC1; then p=

### Testing DMARC

1. Publish DMARC record at `_dmarc.yourdomain.com`
2. Send test emails
3. Wait 24-48 hours for reports
4. Review reports using DMARC analyzer tool
5. Verify SPF and DKIM alignment
6. Gradually increase policy strictness

---

## How They Work Together

### The Complete Authentication Flow

When someone receives an email from you, here's what happens:

**1. SPF Check:**
- Is the sending server's IP authorized for this domain? 
- Check the SPF record

**2. DKIM Check:**
- Is there a valid DKIM signature?
- Verify it using the public key in DNS

**3. DMARC Check:**
- Does SPF pass AND align with the From address? OR
- Does DKIM pass AND align with the From address?
- If YES to either → DMARC passes ✓
- If NO to both → Apply DMARC policy (none/quarantine/reject)

**To pass DMARC, you need:**
- At least ONE of SPF or DKIM to pass
- That passing method must be ALIGNED with the From domain

### Example Scenarios

**Scenario 1: Everything configured correctly**
```
From: user@example.com
SPF: Passes ✓ (IP authorized, aligns with example.com)
DKIM: Passes ✓ (signature valid, d=example.com)
DMARC: Passes ✓ (both aligned)
Result: Email delivered to inbox
```

**Scenario 2: Attacker spoofing your domain**
```
From: ceo@yourcompany.com (fake)
SPF: Fails ✗ (attacker's IP not in your SPF)
DKIM: Fails ✗ (no valid signature)
DMARC: Fails ✗ (neither passes)
Your DMARC policy: p=reject
Result: Email rejected, never delivered ✓
```

**Scenario 3: Forwarded email**
```
From: user@example.com
SPF: Fails ✗ (forwarding server's IP not in your SPF)
DKIM: Passes ✓ (signature still valid after forwarding)
DMARC: Passes ✓ (DKIM aligned)
Result: Email delivered to inbox ✓
```

**Scenario 4: Misconfigured third-party service**
```
From: marketing@example.com
SPF: Passes ✓ (third-party IP in SPF)
DKIM: Passes ✓ but d=thirdparty.com (NOT aligned)
DMARC: Fails ✗ (DKIM not aligned, SPF would need to pass)
Your DMARC policy: p=quarantine
Result: Email goes to spam folder
Fix: Configure third-party service to use d=example.com for DKIM
```

---

## Practical Implementation Steps

### Step-by-Step Setup Guide

**Week 1-2: Prepare**
1. List all services that send email for your domain
   - Website contact forms
   - Marketing platforms (Mailchimp, HubSpot, etc.)
   - Help desk (Zendesk, Freshdesk, etc.)
   - CRM (Salesforce, etc.)
   - Transactional email (SendGrid, Mailgun, etc.)
   - Your mail server

**Week 2-3: Configure SPF**
1. Create SPF record including all authorized sources
2. Keep under 10 DNS lookups
3. Use ~all (softfail) while testing
4. Publish to DNS
5. Test with SPF checker tool
6. Send test emails, verify headers

**Week 3-4: Configure DKIM**
1. Generate keys (or your hosting provider does this)
2. Publish public key in DNS
3. Configure mail server to sign emails
4. Test by sending email and checking headers
5. Verify signature validates correctly

**Week 4-6: Start DMARC Monitoring**
1. Publish DMARC record with p=none
2. Set up email address for reports (rua=)
3. Wait for reports to arrive (24-48 hours)
4. Use DMARC analyzer tool to review
5. Identify any failures
6. Fix SPF/DKIM configuration issues

**Week 6-12: Move to p=quarantine**
1. Verify reports show most email passing
2. Update DMARC to p=quarantine
3. Monitor for legitimate emails going to spam
4. Fix any new issues discovered
5. Build confidence over several weeks

**Week 12+: Reach p=reject**
1. Verify reports show consistent passing
2. Update DMARC to p=reject
3. Continue monitoring reports
4. Respond quickly to any new failures
5. Maintain and monitor ongoing

### Ongoing Maintenance

**Monthly:**
- Review DMARC reports
- Check for new unauthorized senders
- Verify legitimate senders still passing

**Every 6 months:**
- Rotate DKIM keys
- Review and update SPF record
- Remove any unused services

**When adding new email service:**
1. Add to SPF record (watch lookup limit)
2. Configure their DKIM to use your domain
3. Send test emails
4. Monitor DMARC reports for 1-2 weeks
5. Verify alignment

---

## Troubleshooting Common Issues

### "My emails are going to spam"

**Check this:**
1. Do you have SPF, DKIM, and DMARC configured?
2. Are SPF and DKIM passing? (check email headers)
3. Is DMARC aligned? (check reports)
4. What's your DMARC policy? (p=none does nothing)
5. Is your sending IP on any blocklists?

**Common causes:**
- Missing SPF record
- DKIM signature failing
- DMARC not aligned
- Sending server not in SPF record
- DKIM using wrong canonicalization (use relaxed/relaxed)

### "SPF is failing"

**Check this:**
1. Is the sending IP in your SPF record?
2. Have you exceeded 10 DNS lookups? (use SPF checker)
3. Is there a syntax error? (use validator)
4. Did a third-party service change their IPs?

**Common fixes:**
- Add missing IP addresses or include statements
- Flatten SPF or use subdomains if over 10 lookups
- Fix syntax errors
- Update for third-party IP changes

### "DKIM is failing"

**Check this:**
1. Is the public key published in DNS at the right location?
2. Does the selector in the email header match DNS?
3. Is the DNS record formatted correctly?
4. Are you using simple/simple canonicalization?

**Common fixes:**
- Publish public key at correct DNS location
- Fix selector mismatch
- Correct DNS syntax errors
- Switch to relaxed/relaxed canonicalization

### "DMARC is failing but SPF and DKIM pass"

**This is an alignment issue!**

**Check this:**
1. What domain is in the visible From address?
2. What domain did SPF authenticate?
3. What domain did DKIM authenticate (d= tag)?
4. Do any of them align with the From domain?

**Common causes:**
- Third-party service using their domain for DKIM
- Bounce address (SPF) different from From address
- Strict alignment when should be relaxed

**Common fixes:**
- Configure third-party to use your domain for DKIM d= tag
- Ensure From address matches your domain
- Use relaxed alignment (not strict)

---

## Key Takeaways

### Must Remember

1. **All three technologies work together**
   - SPF validates sending server
   - DKIM validates message authenticity
   - DMARC requires alignment and sets policy

2. **SPF: Don't exceed 10 DNS lookups**
   - Most common technical problem
   - Use subdomains or explicit IPs if needed

3. **DKIM: Use relaxed/relaxed canonicalization**
   - Simple/simple breaks too easily
   - Rotate keys every 6 months

4. **DMARC: Follow the phases**
   - Start with p=none (monitoring)
   - Move to p=quarantine (intermediate)
   - Reach p=reject (maximum protection)
   - Don't skip steps!

5. **Alignment is what makes DMARC effective**
   - At least one of SPF or DKIM must align
   - Use relaxed alignment (not strict)
   - Configure third-party services correctly

6. **Monitor continuously**
   - Read DMARC reports regularly
   - Use DMARC analyzer tools
   - Respond to failures quickly
   - Maintain configurations

### Your Goal

By the end of your training, you should be able to:
- ✓ Create and maintain SPF records under 10 lookups
- ✓ Set up and verify DKIM signing
- ✓ Implement DMARC from p=none to p=reject
- ✓ Read and understand DMARC reports
- ✓ Troubleshoot authentication failures
- ✓ Configure third-party email services correctly
- ✓ Protect both main domain and subdomains

### Resources and Tools

**Testing and Validation:**
- MxToolbox (SPF, DKIM, DMARC checkers)
- EasyDMARC (validation and reporting)
- PowerDMARC (analysis tools)
- Google Admin Toolbox (message header analyzer)

**DMARC Report Analyzers:**
- EasyDMARC
- PowerDMARC  
- Valimail
- DMARCian

**When to Ask for Help:**
- SPF exceeding 10 lookups and not sure how to fix
- DKIM signatures consistently failing
- DMARC reports showing unexpected failures
- Legitimate email being blocked after moving to p=reject
- Complex subdomain or multi-service configurations

---

## Practice Exercises

### Exercise 1: SPF Record Creation

**Scenario**: You run example.com and send email from:
- Your hosting provider's server (IP: 192.0.2.1)
- Google Workspace (use include:_spf.google.com)
- SendGrid for newsletters (use include:sendgrid.net)

**Task**: Write the SPF record

**Answer:**
```
v=spf1 ip4:192.0.2.1 include:_spf.google.com include:sendgrid.net ~all
```

### Exercise 2: DKIM DNS Location

**Scenario**: Your selector is "mail2026" and your domain is "example.com"

**Task**: Where should you publish the DKIM public key?

**Answer:**
```
mail2026._domainkey.example.com
```

### Exercise 3: DMARC Policy Choice

**Scenario**: You just set up SPF and DKIM. DMARC reports show 95% of email passing, 5% failing (forwarded emails). What policy should you use?

**Answer:**
- Start with `p=none` to monitor
- After confirming the 5% is only forwarding (not misconfig), move to `p=quarantine`
- Eventually move to `p=reject` (DKIM handles forwarding)

### Exercise 4: Alignment Diagnosis

**Scenario**: Email headers show:
- From: marketing@example.com
- SPF: Passes (authenticates mailserver.example.com)
- DKIM: Passes (d=sendgrid.net)
- DMARC: Fails

**Task**: Why does DMARC fail? How do you fix it?

**Answer:**
- DMARC fails because neither SPF nor DKIM align with example.com
- SPF authenticates mailserver.example.com (relaxed alignment: passes ✓)
- Wait, SPF should align! Check if you're using strict alignment
- DKIM authenticates d=sendgrid.net (doesn't align with example.com)
- **Fix**: Configure SendGrid to use d=example.com for DKIM signing

---

## Summary

Email authentication isn't optional anymore—it's essential for email deliverability and security. By implementing SPF, DKIM, and DMARC correctly and following the phased approach, you'll:

- Ensure your legitimate emails reach the inbox
- Protect your domain from being spoofed
- Build trust with email providers
- Meet industry requirements
- Monitor and control who sends email for your domain

Take your time, follow the phases, monitor continuously, and don't hesitate to ask questions. Email authentication mastery comes through understanding and practice!

Good luck with your learning journey!