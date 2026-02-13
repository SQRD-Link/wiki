# Email Deliverability FAQ - Customer Guide

## Introduction

Having trouble with your emails going to spam? Wondering why email providers are asking you to configure SPF, DKIM, and DMARC? This FAQ answers the most common questions our customers ask about email deliverability and authentication.

---

## General Questions

### What is email authentication and why do I need it?

Email authentication proves that emails claiming to come from your domain are actually sent by you (not spammers or scammers). It uses three technologies:

- **SPF** - Lists which servers can send email for your domain
- **DKIM** - Adds a digital signature to prove emails are authentic
- **DMARC** - Tells email providers what to do if emails fail authentication checks

**You need it because:**
- Major email providers (Gmail, Yahoo, Outlook) now require it for bulk sending
- Without it, your emails may go to spam or be rejected entirely
- It protects your domain from being used for phishing attacks
- It significantly improves email deliverability

### Do I really need to set this up, or is it optional?

**It's essentially required now.** As of February 2024, Google and Yahoo require SPF, DKIM, and DMARC for anyone sending bulk email (500+ messages per day). Microsoft and other providers have similar requirements.

Even if you send less than 500 emails per day, we strongly recommend setting it up because:
- Your emails will be more likely to reach the inbox
- You protect your domain reputation
- You prevent others from sending fake emails using your domain name

### Will my emails stop working if I don't set this up?

Maybe. It depends on:

**If you send bulk email (500+ per day):** Yes, Gmail and Yahoo will likely reject or spam your emails starting in 2024.

**If you send occasional emails:** Your emails might still deliver, but:
- They're more likely to go to spam
- Some recipients may not receive them at all
- Your domain is vulnerable to spoofing

**Bottom line:** Set it up to be safe and maintain good deliverability.

### I'm not very technical. Is this hard to set up?

**Good news:** We've made it as simple as possible for you.

**What we handle automatically:**
- DKIM key generation and signing
- Basic SPF configuration for our mail servers
- Guidance through the DMARC setup process

**What you need to do:**
- Add DNS records (we'll give you exact values to copy/paste)
- List any third-party email services you use (like Mailchimp, SendGrid, etc.)
- Monitor DMARC reports (we provide tools to make this easy)

**Time required:** Usually 30-60 minutes for initial setup.

### How much does it cost?

**Basic email authentication is FREE** and included with your hosting plan. We provide:
- Automatic DKIM signing
- SPF configuration
- Basic DMARC reporting

**Optional paid tools:**
- Advanced DMARC report analysis dashboards (~$10-50/month)
- Professional email deliverability monitoring services

Most customers only need the free basic features.

---

## SPF Questions

### What is SPF?

SPF (Sender Policy Framework) is a DNS record that lists which mail servers are allowed to send email on behalf of your domain.

**Simple example:**
"Only these servers can send email for mycompany.com: my hosting provider's server and Google Workspace."

### How do I set up SPF?

**Step 1:** Log into your hosting control panel

**Step 2:** Go to DNS management for your domain

**Step 3:** Add a new TXT record:
- **Name/Host:** @ (or leave blank for root domain)
- **Type:** TXT
- **Value:** We'll provide this based on your configuration

**Typical SPF record example:**
```
v=spf1 include:mail.yourhosting.com include:_spf.google.com ~all
```

**Step 4:** Save the record and wait 24-48 hours for DNS propagation

### What should I include in my SPF record?

Include every service that sends email using your domain:

**Common services:**
- ✓ Your hosting provider (we'll give you the include statement)
- ✓ Google Workspace: `include:_spf.google.com`
- ✓ Microsoft 365: `include:spf.protection.outlook.com`
- ✓ Mailchimp: `include:servers.mcsv.net`
- ✓ SendGrid: `include:sendgrid.net`
- ✓ Constant Contact: `include:spf.constantcontact.com`
- ✓ HubSpot: `include:_spf.hubspot.com`
- ✓ Zendesk: `include:mail.zendesk.com`

**Not sure what services you use?** Check your DMARC reports after enabling monitoring mode.

### I got an error about "too many DNS lookups." What does that mean?

SPF has a limit of 10 DNS lookups. Each `include:` statement counts as a lookup, and some services have nested lookups that count toward your limit.

**Example of hitting the limit:**
If you use 8 different email services, each with an `include:` statement, you might exceed 10 lookups total.

**Solutions:**
1. **Use subdomains** - Set up marketing.yourdomain.com, support.yourdomain.com, etc.
2. **Contact us** - We can help restructure your SPF record
3. **Remove unused services** - Only include services you actively use

**Need help?** Contact our support team - we can check your SPF record and optimize it.

### What does the "~all" or "-all" at the end mean?

This tells email servers what to do with emails from servers NOT listed in your SPF record:

- **~all (softfail)** - Accept but mark as suspicious (RECOMMENDED while testing)
- **-all (fail)** - Reject emails from unauthorized servers (use after testing)
- **?all (neutral)** - No opinion (NOT recommended)

**Our recommendation:**
1. Start with `~all` while you're testing
2. Switch to `-all` after you've confirmed everything works

### How do I test if my SPF is working?

**Method 1: Use online tools**
- Visit MxToolbox.com/spf
- Enter your domain name
- Check for errors and lookup count

**Method 2: Send a test email**
- Send an email from your domain to your Gmail account
- Open the email in Gmail
- Click the three dots menu → "Show original"
- Look for "SPF: PASS"

**Method 3: Contact support**
- We can check your SPF configuration for you
- We'll verify it's set up correctly

---

## DKIM Questions

### What is DKIM?

DKIM (DomainKeys Identified Mail) adds an invisible digital signature to your emails. This signature proves:
1. The email really came from your domain
2. The email hasn't been tampered with during delivery

Think of it like a tamper-proof seal on a package.

### Do I need to set up DKIM myself?

**Good news: We handle most of this automatically!**

**What we do:**
- Generate your unique DKIM keys
- Automatically sign all emails sent through our servers
- Manage key rotation for security

**What you do:**
- Publish a DNS record with your DKIM public key (we provide the exact record)

**If you use third-party email services** (Mailchimp, SendGrid, etc.):
- They'll provide their own DKIM records to add
- You'll need to add those separately

### How do I add the DKIM DNS record?

**Step 1:** Get your DKIM public key
- Log into your hosting control panel
- Navigate to Email → DKIM settings
- Copy the DNS record we provide

**Step 2:** Add to DNS
- Go to DNS management
- Add a new TXT record
- **Name/Host:** Something like `default._domainkey` (we'll specify)
- **Value:** The long public key string we provide

**Step 3:** Verify
- Use our verification tool, or
- Send a test email and check headers for "DKIM: PASS"

### What if I use multiple email services?

**Each service needs its own DKIM record.**

**Example:** If you use both our hosting email AND Mailchimp:
- Add our DKIM record (we provide)
- Add Mailchimp's DKIM record (they provide)
- Both will coexist in your DNS

Each service signs emails with their own DKIM key, so you need both records published.

### How often do I need to update DKIM?

**For emails sent through our servers:** We handle updates automatically. You don't need to do anything.

**For third-party services:** They usually handle updates automatically too.

**Security best practice:** DKIM keys should be rotated every 6 months, but we manage this for you.

### My DKIM verification is failing. What should I check?

**Common issues:**

1. **DNS record not published yet**
   - Wait 24-48 hours after adding the record
   - Check with a DNS lookup tool

2. **Typo in the DNS record**
   - The DKIM key is very long - even one wrong character breaks it
   - Copy/paste carefully, don't type it manually

3. **Wrong DNS record name**
   - Must match exactly: `selector._domainkey.yourdomain.com`
   - Check that you used the right selector name

4. **Using a third-party service**
   - Make sure you added their DKIM record, not ours
   - Check their documentation for the exact record

**Still having issues?** Contact our support - we can diagnose DKIM problems quickly.

---

## DMARC Questions

### What is DMARC?

DMARC (Domain-based Message Authentication, Reporting & Conformance) is the "policy layer" that:

1. Checks if your SPF and DKIM are set up correctly
2. Tells email providers what to do if authentication fails
3. Sends you daily reports about emails sent using your domain

Think of DMARC as the manager overseeing SPF and DKIM.

### Why is DMARC important?

**DMARC is now required by Gmail and Yahoo** for bulk senders (500+ emails/day).

Beyond compliance, DMARC:
- Gives you visibility into who's sending email using your domain
- Protects against phishing and spoofing attacks
- Significantly improves email deliverability
- Alerts you to authentication problems before they impact delivery

### How do I set up DMARC?

**Step 1:** Make sure SPF and DKIM are configured first

**Step 2:** Add a DMARC DNS record:
- **Name/Host:** `_dmarc`
- **Type:** TXT
- **Value:** Start with this:
  ```
  v=DMARC1; p=none; rua=mailto:dmarc@yourdomain.com;
  ```

**Step 3:** Set up an email address to receive reports
- Create `dmarc@yourdomain.com` or use our reporting service

**Step 4:** Monitor reports for 2-4 weeks

**Step 5:** Gradually increase enforcement (we'll guide you)

### What do the DMARC policies mean (p=none, p=quarantine, p=reject)?

**p=none (Monitoring Mode)**
- Email providers do nothing with failed emails
- You just receive reports
- **Use this first** to see what's happening
- Provides NO protection, only visibility

**p=quarantine (Intermediate)**
- Email providers send failed emails to spam
- Provides some protection
- Good middle step

**p=reject (Maximum Protection)**
- Email providers reject failed emails completely
- Maximum protection against spoofing
- **This is your ultimate goal**
- Only use after thorough testing

**Our recommendation:**
1. Start with `p=none` for 2-4 weeks
2. Move to `p=quarantine` for 2-4 weeks
3. Finally move to `p=reject` when confident

**Never skip steps!** Jumping to p=reject too quickly can block legitimate emails.

### What are DMARC reports and what do I do with them?

**DMARC reports** are daily XML files that show:
- Who's sending email using your domain
- Which emails passed authentication
- Which emails failed and why
- Source IP addresses and volumes

**What to do with them:**

**Option 1: Use our free analyzer tool**
- We parse the XML and show you easy-to-read dashboards
- Identifies legitimate vs. suspicious senders
- Highlights authentication issues

**Option 2: Use a third-party service**
- Tools like EasyDMARC, PowerDMARC, Valimail
- Usually $10-50/month
- More advanced features

**What to look for in reports:**
- ✓ Legitimate services passing authentication (good!)
- ⚠ Legitimate services failing (needs configuration fix)
- ✗ Unknown sources sending for your domain (potential spoofing)

### Do I need to do anything with DMARC after setting it up?

**Yes - ongoing monitoring is important:**

**Weekly:**
- Check DMARC reports for any new failures
- Verify legitimate email is passing

**Monthly:**
- Review overall trends
- Ensure no new unauthorized senders appear

**When adding new email services:**
- Add them to SPF
- Configure their DKIM
- Monitor DMARC reports to verify they pass

**When changing policy:**
- We'll guide you through p=none → p=quarantine → p=reject
- Each step requires monitoring before moving forward

### I'm seeing failed DMARC reports for legitimate emails. What's wrong?

**Common causes:**

**1. Email forwarding**
- Forwarded emails often fail SPF
- Solution: Make sure DKIM is configured (it survives forwarding)

**2. Third-party service not configured correctly**
- Service not in your SPF record
- Service DKIM not using your domain
- Solution: Add to SPF and configure their DKIM properly

**3. Mailing lists**
- Some mailing lists modify emails, breaking DKIM
- Solution: Usually acceptable - these are known forwarding services

**4. Alignment issues**
- Email sent through one domain but "From" shows another
- Solution: Ensure services send using your domain name

**Need help diagnosing?** Send us the DMARC report - we can identify the issue.

### Should I set up DMARC for my subdomains?

**Yes! This is important.**

**Common mistake:** Protecting main domain but forgetting subdomains.

**Example vulnerability:**
- `yourdomain.com` has `p=reject` (protected)
- `mail.yourdomain.com` has no DMARC (VULNERABLE!)
- Scammers can send from `support@mail.yourdomain.com`

**Solution options:**

**Option 1:** Set subdomain policy in main DMARC record
```
v=DMARC1; p=reject; sp=reject; rua=mailto:dmarc@yourdomain.com;
```
The `sp=reject` applies the same policy to all subdomains.

**Option 2:** Create separate DMARC records for each subdomain
- `_dmarc.mail.yourdomain.com`
- `_dmarc.marketing.yourdomain.com`

**We recommend Option 1** for simplicity - protects all subdomains automatically.

---

## Email Service Integration

### I use Google Workspace / Microsoft 365. Do I still need to set this up?

**Yes, you still need to configure authentication for your domain.**

**Google Workspace:**
- Add to SPF: `include:_spf.google.com`
- Set up DKIM through Google Admin console
- Configure DMARC for your domain

**Microsoft 365:**
- Add to SPF: `include:spf.protection.outlook.com`
- Enable DKIM through Microsoft 365 admin center
- Configure DMARC for your domain

**The good news:** These services make it relatively easy with step-by-step guides.

**Need help?** We can verify your configuration is correct.

### I use Mailchimp / Constant Contact / SendGrid for newsletters. What do I need to do?

**For each third-party email service:**

**Step 1: Add to SPF**
- Mailchimp: `include:servers.mcsv.net`
- Constant Contact: `include:spf.constantcontact.com`
- SendGrid: `include:sendgrid.net`

**Step 2: Configure DKIM in their platform**
- Log into their service
- Find "Authentication" or "DKIM" settings
- Follow their instructions to add DKIM DNS records
- **Important:** Make sure they use YOUR domain name for DKIM

**Step 3: Verify authentication**
- Send test emails
- Check email headers for SPF and DKIM pass
- Check DMARC reports after 48 hours

**Step 4: Configure "From" address**
- Use an email address on your domain: `newsletters@yourdomain.com`
- Not their domain: ~~`you@sendgrid.com`~~

### How do I know which email services I'm currently using?

**Check these places:**

**Website forms:**
- Contact forms
- Newsletter signups
- E-commerce order confirmations

**Marketing:**
- Email marketing platforms
- Marketing automation
- CRM systems

**Support:**
- Help desk software
- Ticket systems
- Live chat platforms

**Transactional:**
- Payment confirmations
- Password resets
- Account notifications

**Not sure?** Enable DMARC with `p=none` and check the reports - they'll show you every service sending for your domain!

### Can I send email from both my hosting and a third-party service?

**Absolutely!** This is very common.

**Example setup:**
- Website contact forms → Your hosting server
- Marketing newsletters → Mailchimp
- Transactional emails → SendGrid
- Support tickets → Zendesk

**What you need:**
- All services added to SPF record
- DKIM configured for each service
- All services using your domain name in "From" address
- DMARC monitoring to ensure all pass

**Best practice:** Use subdomains for different purposes:
- `support@yourdomain.com` → Help desk
- `newsletters@yourdomain.com` → Marketing
- `noreply@yourdomain.com` → Transactional

---

## Troubleshooting

### My emails are going to spam after setting up DMARC. What happened?

**Don't panic - this is usually a configuration issue:**

**Check these first:**

1. **Did you move to p=reject too quickly?**
   - Solution: Go back to p=none or p=quarantine
   - Monitor reports for 2-4 weeks before moving to p=reject

2. **Is a service missing from your SPF record?**
   - Check DMARC reports to identify the service
   - Add it to your SPF record

3. **Is DKIM configured correctly for all services?**
   - Test each service by sending emails
   - Check headers for DKIM pass

4. **Alignment issues?**
   - Make sure "From" address uses your domain
   - Ensure services are configured to use your domain for DKIM

**Still having issues?** Contact us with a DMARC report - we can diagnose the problem.

### How long does DNS propagation take?

**Typical propagation time:** 24-48 hours

**What this means:**
- You add a DNS record today
- It may take up to 48 hours to work everywhere
- Most places see it within a few hours

**Why it varies:**
- Different DNS servers cache records for different times
- Some ISPs update faster than others
- Your DNS TTL (Time To Live) setting affects speed

**Checking propagation:**
- Use whatsmydns.net
- Enter your domain and record type
- See which global DNS servers have your record

**Pro tip:** Lower your DNS TTL to 300 seconds a day before making changes, then raise it back to 3600 after changes propagate.

### I'm getting "SPF PermError" - what does that mean?

**PermError = Permanent Error** - something is fundamentally wrong with your SPF record.

**Common causes:**

1. **Too many DNS lookups (exceeded 10)**
   - Use an SPF checker to count lookups
   - Solution: Simplify record or use subdomains

2. **Syntax error in SPF record**
   - Missing space, extra character, typo
   - Solution: Validate with an SPF checker tool

3. **Invalid mechanism**
   - Malformed IP address: ~~`ip4:192.0.2`~~ should be `ip4:192.0.2.1`
   - Solution: Carefully check each mechanism

4. **Multiple SPF records**
   - You can only have ONE SPF record per domain
   - Solution: Combine into a single record

**How to fix:**
- Copy your SPF record
- Paste into MxToolbox.com/spf
- It will identify the specific error
- Fix and republish

### I changed my DNS records but nothing is working yet. How long should I wait?

**Give it 48 hours** before troubleshooting.

**Timeline:**

**0-6 hours:**
- Some DNS servers updated
- May work intermittently

**6-24 hours:**
- Most DNS servers updated
- Should work for most people

**24-48 hours:**
- Global propagation complete
- Should work everywhere

**After 48 hours and still not working?**
- Double-check the DNS record is actually published
- Use DNS lookup tools to verify
- Check for typos or formatting errors
- Contact our support for help

### Can I test my setup before going live?

**Yes! Here's how:**

**Step 1: Set up with p=none**
- No enforcement, just monitoring
- Safe to enable while you test

**Step 2: Send test emails**
- Send to your Gmail, Yahoo, Outlook accounts
- Check if they arrive in inbox (not spam)

**Step 3: Check email headers**
- View the email source/headers
- Look for:
  - `SPF: PASS`
  - `DKIM: PASS`  
  - `DMARC: PASS`

**Step 4: Wait for DMARC reports**
- Reports arrive after 24-48 hours
- Check if your test emails show as passing

**Step 5: Review and refine**
- Fix any failures found in reports
- Test again
- Only increase DMARC policy when everything passes

**Need help testing?** We offer free authentication checks - just ask!

---

## Common Scenarios

### I'm a small business owner with a simple website. Do I really need all this?

**Short answer: Yes, but it's simpler than you think.**

**Your likely setup:**
- Contact form on your website
- Maybe occasional emails to customers

**What you need to do:**
1. We'll configure SPF and DKIM automatically
2. You add one DMARC DNS record (we'll give you the exact value)
3. Check reports once a month to make sure everything works

**Time required:** 15-30 minutes initial setup, 5 minutes per month ongoing

**Why you should do it:**
- Google and Yahoo require it now
- Your contact form emails won't go to spam
- Your domain is protected from scammers
- It's free with your hosting

### I'm an online store. How does this affect my order confirmation emails?

**Critical for e-commerce!** Order confirmations must reach customers.

**What you need:**

**If using our hosting for transactional emails:**
- We handle SPF and DKIM automatically
- You add DMARC record
- Done!

**If using Shopify, WooCommerce, or similar:**
- Add their SPF include statement
- Configure their DKIM
- Ensure they send from your domain: `orders@yourstore.com`
- NOT from their domain: ~~`yourstore@shopify.com`~~

**Payment processor emails (Stripe, PayPal):**
- These send from their own domains
- You don't need to configure anything for those

**Testing:**
- Place a test order
- Check if confirmation email arrives
- Check email headers for authentication pass
- Monitor DMARC reports

### I send email newsletters to thousands of subscribers. What do I need?

**Bulk senders (500+ emails/day) have mandatory requirements:**

**Google and Yahoo require:**
1. ✓ SPF configured
2. ✓ DKIM configured  
3. ✓ DMARC with at least p=none
4. ✓ One-click unsubscribe link
5. ✓ Keep spam complaints under 0.3%

**Our recommendations:**

**Use a professional email marketing service:**
- Mailchimp, Constant Contact, SendGrid, etc.
- They handle deliverability infrastructure
- They provide authentication setup guides

**Configure authentication:**
- Add their SPF include to your record
- Set up DKIM in their platform
- Use your domain for "From" address
- Set DMARC to p=quarantine or p=reject (after testing)

**Monitor deliverability:**
- Check DMARC reports weekly
- Monitor bounce rates
- Track spam complaints
- Keep your list clean (remove bounces and unsubscribes)

**Need help?** We can review your setup and ensure compliance.

### I have multiple domains. Do I need to set this up for each one?

**Yes - each domain needs its own configuration.**

**Example:**
- `mycompany.com` → Needs SPF, DKIM, DMARC
- `mycompany.net` → Needs SPF, DKIM, DMARC  
- `mycompany.org` → Needs SPF, DKIM, DMARC

**Why?**
- Each domain is evaluated independently
- DNS records are per-domain
- You can't share authentication between domains

**Good news:**
- If domains use the same email services, configuration is similar
- We can help you set up multiple domains efficiently
- Most control panels make it easy to duplicate settings

**Best practice:**
- Choose one primary domain for email
- Configure all domains, but mostly use the primary one
- Forward emails from other domains to your primary

---

## Getting Help

### How can I check if my email authentication is set up correctly?

**Self-service options:**

**Online tools:**
- MxToolbox.com - Check SPF, DKIM, DMARC
- EasyDMARC.com - Comprehensive testing
- Google Admin Toolbox - Email header analysis

**Send test emails:**
- Send to your Gmail account
- Click three dots → "Show original"
- Check for SPF, DKIM, DMARC all showing "PASS"

**Our tools:**
- Login to your control panel
- Navigate to Email → Authentication Check
- We'll verify your configuration

### What information should I provide when asking for help?

**To help us help you faster, include:**

1. **Your domain name**
2. **What you're trying to do**
   - "Set up SPF for Mailchimp"
   - "Fix DMARC failures"
   - "Emails going to spam"

3. **Email services you use**
   - Hosting email
   - Google Workspace
   - Mailchimp
   - etc.

4. **Any error messages**
   - SPF PermError
   - DKIM verification failed
   - DMARC reports showing failures

5. **Current DNS records** (if you know them)
   - Your SPF record
   - DKIM records
   - DMARC record

6. **Sample DMARC report** (if applicable)
   - We can diagnose issues from reports

### Do you offer email authentication setup as a service?

**Yes! We offer several support options:**

**Free (included with hosting):**
- Email support for basic questions
- Configuration verification
- Troubleshooting guidance
- Documentation and guides

**Premium setup service ($49-99 one-time):**
- We audit your current setup
- Configure SPF, DKIM, DMARC correctly
- Set up DMARC monitoring
- Test and verify everything works
- Provide written documentation

**Managed email deliverability ($29/month):**
- We handle all authentication
- Monitor DMARC reports for you
- Proactive issue detection
- Monthly deliverability reports
- Priority support

**Contact us** to discuss which option is right for you.

### Where can I learn more?

**Our resources:**
- Knowledge base: help.yourhosting.com/email-authentication
- Video tutorials: yourhosting.com/videos
- Monthly webinars on email deliverability
- Live chat support during business hours

**External resources:**
- DMARC.org - Official DMARC site
- MxToolbox.com - Testing tools and guides
- Google's email sender guidelines
- M3AAWG best practices

**Industry blogs:**
- Valimail blog (DMARC expertise)
- Postmark guides (email deliverability)
- Return Path resources (inbox placement)

---

## Quick Reference

### Quick Setup Checklist

- [ ] **Week 1: Configure SPF**
  - [ ] List all email services you use
  - [ ] Create SPF record including all services
  - [ ] Verify under 10 DNS lookups
  - [ ] Add SPF TXT record to DNS
  - [ ] Test with SPF checker tool

- [ ] **Week 2: Configure DKIM**
  - [ ] Get DKIM record from hosting control panel
  - [ ] Add DKIM TXT record to DNS
  - [ ] Configure DKIM for third-party services
  - [ ] Send test emails
  - [ ] Verify DKIM passes in headers

- [ ] **Week 3: Start DMARC Monitoring**
  - [ ] Add DMARC record with p=none
  - [ ] Set up email for reports
  - [ ] Wait 48 hours for first reports
  - [ ] Review reports using analyzer tool
  - [ ] Identify any failing emails

- [ ] **Week 4-8: Fix Issues**
  - [ ] Add missing services to SPF
  - [ ] Fix DKIM configuration issues
  - [ ] Ensure all services use your domain
  - [ ] Verify alignment in DMARC reports
  - [ ] Monitor weekly

- [ ] **Week 8-12: Increase Enforcement**
  - [ ] Change DMARC to p=quarantine
  - [ ] Monitor for legitimate email in spam
  - [ ] Fix any new issues
  - [ ] Wait 4+ weeks

- [ ] **Week 12+: Maximum Protection**
  - [ ] Change DMARC to p=reject
  - [ ] Continue monitoring reports
  - [ ] Maintain ongoing

### Common DNS Records Reference

**SPF Record:**
```
Name: @ (or blank)
Type: TXT
Value: v=spf1 include:mail.yourhosting.com ~all
```

**DKIM Record:**
```
Name: default._domainkey
Type: TXT
Value: v=DKIM1; k=rsa; p=MIGfMA0GCSqGSI... (we provide)
```

**DMARC Record:**
```
Name: _dmarc
Type: TXT
Value: v=DMARC1; p=none; rua=mailto:dmarc@yourdomain.com;
```

### Support Contact Information

**Email:** support@yourhosting.com
**Live Chat:** Available 9am-5pm EST Mon-Fri
**Phone:** 1-800-HOSTING (business hours)
**Ticket System:** portal.yourhosting.com

**For urgent deliverability issues:**
- Use "Priority" ticket flag
- Call during business hours
- Include "email authentication" in subject line

---

## Final Thoughts

Email authentication might seem complex, but it's essential for modern email deliverability. The good news is that we're here to help every step of the way.

**Remember:**
- ✓ Start with p=none (monitoring)
- ✓ Take your time progressing through phases
- ✓ Monitor reports regularly
- ✓ Ask for help when needed
- ✓ Don't rush to p=reject

**Most importantly:** Better to set it up correctly and gradually than to rush and accidentally block legitimate emails.

**Questions?** Contact our support team - we're here to help you succeed!

---

*Last updated: February 2026*
*This FAQ is regularly updated to reflect current email authentication requirements and best practices.*