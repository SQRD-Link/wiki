# Email Deliverability Research - Sources for NotebookLM

This document contains all the sources used in the email deliverability deep dive research on SPF, DKIM, and DMARC. These sources can be uploaded to NotebookLM for further analysis and question answering.

---

## Primary Sources

### SPF (Sender Policy Framework)

1. **Wikipedia - Sender Policy Framework**
   - URL: https://en.wikipedia.org/wiki/Sender_Policy_Framework
   - Description: Comprehensive overview of SPF protocol and functionality

2. **Cloudflare Learning - DNS SPF Record**
   - URL: https://www.cloudflare.com/learning/dns/dns-records/dns-spf-record/
   - Description: Educational resource on SPF DNS records and implementation

3. **EasyDMARC - SPF Record Syntax Structure and Components**
   - URL: https://easydmarc.com/blog/spf-record-syntax-structure-and-components/
   - Description: Detailed breakdown of SPF record syntax and mechanisms

4. **DuoCircle - SPF Macro Explained: Practical Guide**
   - URL: https://www.duocircle.com/email-security/spf-macro-explained-practical-guide-overcoming-10-dns-lookup-limit
   - Description: Advanced SPF macros for overcoming DNS lookup limitations

5. **MxToolbox - SPF Included Lookups Problem**
   - URL: https://mxtoolbox.com/problem/spf/spf-included-lookups
   - Description: Troubleshooting SPF lookup limit issues

6. **Valimail Blog - Approaching SPF Limit**
   - URL: https://www.valimail.com/blog/approaching-spf-limit/
   - Description: Strategies for managing SPF lookup limits

7. **DuoCircle - SPF Records: Fix 10 DNS Lookup Limit Issues**
   - URL: https://www.duocircle.com/spf-records/avoid-spf-failures-fix-10-dns-lookup-limit-issues-records
   - Description: Solutions for SPF DNS lookup constraints

8. **DMARCian - SPF Best Practices**
   - URL: https://dmarcian.com/spf-best-practices/
   - Description: Industry best practices for SPF implementation

9. **PowerDMARC - Why SPF Authentication Fails**
   - URL: https://powerdmarc.com/why-spf-authentication-fails/
   - Description: Common SPF failure scenarios and solutions

10. **RFC 7208 - SPF Specification**
    - URL: https://datatracker.ietf.org/doc/html/rfc7208
    - Description: Official SPF protocol specification

11. **PowerDMARC - SPF Macros: Everything You Need to Know**
    - URL: https://powerdmarc.com/spf-macros-everything-you-need-to-know/
    - Description: Comprehensive guide to SPF macro usage

12. **DuoCircle - SPF PermError and TempError**
    - URL: https://www.duocircle.com/content/spf-permerror/spf-temperror
    - Description: Understanding and troubleshooting SPF errors

### DKIM (DomainKeys Identified Mail)

13. **Darktrace - DKIM Glossary**
    - URL: https://www.darktrace.com/cyber-ai-glossary/domainkeys-identified-mail-dkim
    - Description: Overview of DKIM technology and concepts

14. **Postmark - DKIM Guide**
    - URL: https://postmarkapp.com/guides/dkim
    - Description: Practical guide to implementing DKIM

15. **M3AAWG - DKIM Key Rotation Best Practices**
    - URL: https://www.m3aawg.org/DKIMKeyRotation
    - Description: Industry standards for DKIM key rotation

16. **DuoCircle - 8 Steps to Implement DKIM Key Rotation Without Downtime**
    - URL: https://www.duocircle.com/email-security/8-steps-to-implement-dkim-key-rotation-without-downtime
    - Description: Practical DKIM key rotation implementation guide

17. **DMARCian - DKIM Failures: Canonicalization**
    - URL: https://dmarcian.com/dkim-failures-canonicalization/
    - Description: Understanding DKIM canonicalization issues

18. **MxToolbox - Troubleshooting DKIM Issues**
    - URL: https://mxtoolbox.com/dmarc/dkim/troubleshooting-dkim-issues
    - Description: Common DKIM problems and solutions

19. **EasyDMARC - DKIM Lookup Tool**
    - URL: https://easydmarc.com/tools/dkim-lookup
    - Description: DKIM record validation and checking

20. **Knowledge Base - DKIM-Signature Header Structure**
    - URL: https://knowledge.broadcom.com/external/article/152351/structure-of-the-dkimsignature-header.html
    - Description: Technical details of DKIM signature headers

21. **EasyDMARC - What is DKIM Key Rotation**
    - URL: https://easydmarc.com/blog/what-is-dkim-key-rotation/
    - Description: Explanation of DKIM key rotation practices

### DMARC (Domain-based Message Authentication, Reporting & Conformance)

22. **DMARC.org - Official Site**
    - URL: https://dmarc.org
    - Description: Official DMARC organization and documentation

23. **Wikipedia - DMARC**
    - URL: https://en.wikipedia.org/wiki/DMARC
    - Description: Comprehensive DMARC overview

24. **DMARC Report - RUA vs RUF Reports**
    - URL: https://dmarcreport.com/blog/dmarc-rua-vs-ruf-reports/
    - Description: Understanding DMARC reporting mechanisms

25. **Valimail Blog - DMARC, DKIM, SPF Explained**
    - URL: https://www.valimail.com/blog/dmarc-dkim-spf-explained/
    - Description: How the three technologies work together

26. **Valimail Blog - What is DMARC Alignment**
    - URL: https://www.valimail.com/blog/what-is-dmarc-alignment/
    - Description: Detailed explanation of DMARC alignment

27. **Valimail Support - DMARC Strict vs Relaxed Alignment**
    - URL: https://support.valimail.com/en/articles/8466455-dmarc-strict-vs-relaxed-alignment
    - Description: Comparison of DMARC alignment modes

28. **Valimail Blog - 7 Common DMARC Mistakes**
    - URL: https://www.valimail.com/blog/7-common-dmarc-mistakes/
    - Description: Common DMARC implementation errors

29. **Valimail Blog - P=none to P=reject Journey**
    - URL: https://www.valimail.com/blog/p-none-to-p-reject/
    - Description: DMARC policy progression guide

30. **DMARCly - How DMARC Works with Subdomains**
    - URL: https://dmarcly.com/blog/how-dmarc-works-with-subdomains-dmarc-sp-tag
    - Description: DMARC subdomain policy configuration

31. **Resend - How DMARC Applies to Subdomains**
    - URL: https://resend.com/blog/how-dmarc-applies-to-subdomains
    - Description: Subdomain DMARC implementation

32. **DuoCircle - DMARC P=Reject Policy: 5 Situations to Implement**
    - URL: https://www.duocircle.com/dmarc/p-reject-dmarc-policy-5-situations-to-implement-it
    - Description: When and how to implement DMARC reject policy

33. **PowerDMARC - What is DMARC Policy**
    - URL: https://powerdmarc.com/what-is-dmarc-policy/
    - Description: Understanding DMARC policy options

34. **EasyDMARC - Understanding DMARC Reports**
    - URL: https://easydmarc.com/blog/understanding-dmarc-reports/
    - Description: How to read and analyze DMARC reports

35. **PowerDMARC - How to Read DMARC Reports**
    - URL: https://powerdmarc.com/how-to-read-dmarc-reports/
    - Description: DMARC report interpretation guide

36. **Suped Knowledge - DMARC RUA and RUF Requirements**
    - URL: https://www.suped.com/knowledge/email-deliverability/technical/what-are-the-requirements-for-rua-and-ruf-in-dmarc-policies
    - Description: DMARC reporting address configuration

37. **DMARCian - Yahoo and Google DMARC Required**
    - URL: https://dmarcian.com/yahoo-and-google-dmarc-required/
    - Description: Major mailbox provider DMARC requirements

38. **OpenSRS Support - Gmail, Microsoft, and Yahoo DMARC Requirements**
    - URL: https://support.opensrs.com/support/solutions/articles/201000063028-the-gmail-microsoft-and-yahoo-dmarc-requirements-on-the-domains-platform
    - Description: 2024/2025 DMARC enforcement requirements

### Integration and Implementation

39. **Mailgun Blog - Email Authentication: Your ID Card for Sending**
    - URL: https://www.mailgun.com/blog/deliverability/email-authentication-your-id-card-sending/
    - Description: How SPF, DKIM, and DMARC work together

40. **Trend Micro Solution - SPF, DKIM, DMARC Authentication Flow**
    - URL: https://success.trendmicro.com/en-US/solution/KA-0010386
    - Description: Technical evaluation sequence for email authentication

41. **GoDaddy Help - Set up SPF, DKIM, or DMARC for Hosting Email**
    - URL: https://www.godaddy.com/help/set-up-spf-dkim-or-dmarc-records-for-my-hosting-email-40810
    - Description: Hosting provider implementation guide

42. **PowerDMARC - Email Authentication: DMARC for ESP**
    - URL: https://powerdmarc.com/email-authentication-dmarc-for-esp/
    - Description: Email service provider DMARC implementation

### Testing and Monitoring

43. **MxToolbox - DMARC Email Tools**
    - URL: https://mxtoolbox.com/dmarc/dmarc-email-tools
    - Description: Suite of DMARC testing and validation tools

44. **EasyDMARC - Best Email Deliverability Tools 2025**
    - URL: https://easydmarc.com/blog/best-email-deliverability-tools-2025/
    - Description: Comprehensive list of email authentication tools

45. **MxToolbox - Email Header Analyzer**
    - URL: https://mxtoolbox.com/Public/Tools/EmailHeaders.aspx
    - Description: Tool for analyzing email authentication headers

### Additional Technologies

46. **BIMI Group - FAQs for Senders and ESPs**
    - URL: https://bimigroup.org/faqs-for-senders-esps/
    - Description: Brand Indicators for Message Identification (builds on DMARC)

47. **Google Support - BIMI Overview**
    - URL: https://support.google.com/a/answer/10911320?hl=en
    - Description: Google's BIMI implementation guide

### DMARC Draft Specifications

48. **DMARC Draft Specification**
    - URL: https://dmarc.org/draft-dmarc-base-00-01.html
    - Description: Technical DMARC protocol specification

---

## Source Categories

### Technical Specifications and RFCs
- RFC 7208 (SPF specification)
- DMARC draft specification
- DKIM-Signature header structure documentation

### Industry Best Practices
- M3AAWG DKIM key rotation guidelines
- DMARCian SPF best practices
- Valimail DMARC implementation guides

### Vendor Documentation and Tools
- MxToolbox (testing and validation tools)
- EasyDMARC (analysis and monitoring)
- PowerDMARC (reporting and enforcement)
- Valimail (alignment and policy guidance)

### Educational Resources
- Wikipedia entries (SPF, DKIM, DMARC)
- Cloudflare Learning Center
- Postmark guides
- Mailgun blog

### Implementation Guides
- GoDaddy hosting setup
- DuoCircle technical guides
- Resend implementation guides
- OpenSRS support articles

### Troubleshooting Resources
- MxToolbox troubleshooting guides
- DMARCian failure analysis
- DuoCircle error resolution
- PowerDMARC debugging guides

---

## How to Use These Sources in NotebookLM

### Upload Strategy

**For comprehensive understanding:**
1. Upload all sources to NotebookLM
2. Let NotebookLM create a unified knowledge base
3. Ask questions across all sources

**For focused learning:**
1. Create separate notebooks for each technology:
   - SPF notebook (sources 1-12)
   - DKIM notebook (sources 13-21)
   - DMARC notebook (sources 22-38)
   - Integration notebook (sources 39-42)
   - Testing notebook (sources 43-45)

### Recommended NotebookLM Queries

**Understanding concepts:**
- "Explain how SPF macros work to overcome the 10 lookup limit"
- "What's the difference between DMARC strict and relaxed alignment?"
- "Why is DKIM canonicalization important?"

**Implementation guidance:**
- "What are the steps for DKIM key rotation without downtime?"
- "How do I progress from DMARC p=none to p=reject safely?"
- "What are the best practices for SPF in multi-tenant hosting?"

**Troubleshooting:**
- "Why would SPF return permerror?"
- "What causes DKIM body hash mismatches?"
- "How do I fix DMARC alignment failures?"

**Advanced topics:**
- "How do email service providers implement DMARC at scale?"
- "What's the relationship between DMARC and BIMI?"
- "How do Google and Yahoo's 2024 DMARC requirements work?"

---

## Source Verification and Updates

**Last Research Date:** February 12, 2026
**Research Tool:** Perplexity Research

**Note:** Email authentication standards and requirements continue to evolve. When using these sources:

1. Verify that industry requirements haven't changed (especially Google/Yahoo enforcement)
2. Check for updated RFC specifications
3. Confirm vendor tool availability and features
4. Validate that best practices remain current

**Key Areas to Monitor for Updates:**
- Major mailbox provider requirements (Google, Yahoo, Microsoft)
- DMARC policy enforcement deadlines
- New authentication technologies (BIMI, ARC, etc.)
- SPF/DKIM/DMARC specification updates
- Industry best practice evolution

---

## Additional Resources Not in Primary Research

### Official Standards Bodies
- IETF (Internet Engineering Task Force) - RFC repository
- M3AAWG (Messaging, Malware and Mobile Anti-Abuse Working Group)
- DMARC.org - Official DMARC organization

### Community Resources
- DMARC subreddit discussions
- Email deliverability forums
- Vendor community support sites

### Continuing Education
- Email authentication webinars
- Industry conference presentations
- Vendor certification programs

---

## Citation Format

When referencing these sources in documentation or presentations:

**For web resources:**
[Source Number] Author/Organization. "Article Title." Website Name. URL. (Accessed: [date])

**Example:**
[1] Wikipedia. "Sender Policy Framework." Wikipedia. https://en.wikipedia.org/wiki/Sender_Policy_Framework (Accessed: February 12, 2026)

**For technical specifications:**
[RFC Number] Author. "RFC Title." RFC Editor. URL. (Published: [date])

**Example:**
[10] RFC 7208. "Sender Policy Framework (SPF) for Authorizing Use of Domains in Email." RFC Editor. https://datatracker.ietf.org/doc/html/rfc7208

---

## Research Methodology Note

This research was conducted using Perplexity's deep research capability with specific focus on:
1. Technical accuracy and depth
2. Current industry best practices
3. Real-world implementation scenarios
4. Troubleshooting and problem-solving guidance
5. Web hosting and ESP-specific considerations

The sources represent a comprehensive cross-section of:
- Official specifications and standards
- Industry vendor expertise
- Academic and educational resources
- Practical implementation guides
- Real-world case studies and examples

All sources were selected for their authority, accuracy, and relevance to email authentication for web hosting environments.