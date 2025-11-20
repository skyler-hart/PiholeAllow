# ABP-Style Pi-hole Quick Reference Guide

## ABP Syntax Overview

**ABP (Adblock Plus)** format provides powerful pattern matching for Pi-hole lists. This format is natively supported by modern Pi-hole versions and offers significant advantages over plain domain lists.

## Basic Syntax

### Allowlist (Whitelist) Entries
```bash
@@||domain.com^           # Allow domain and all subdomains
```
- `@@` = Allowlist marker (creates exception rule)
- `||` = Domain anchor (matches domain exactly)
- `^` = Separator (end of domain/start of path)

### Blocklist Entries
```bash
||domain.com^             # Block domain and all subdomains
```
- `||` = Domain anchor (matches domain exactly) 
- `^` = Separator (end of domain/start of path)

## Domain Matching Examples

### Basic Domain Blocking
```bash
# Block exact domain and all subdomains
||example.com^
# Blocks: example.com, www.example.com, api.example.com, mail.example.com

# Block specific subdomain only
||ads.example.com^
# Blocks: ads.example.com, but NOT example.com or www.example.com
```

### Basic Domain Allowing
```bash
# Allow exact domain and all subdomains
@@||google.com^
# Allows: google.com, www.google.com, mail.google.com, drive.google.com

# Allow specific subdomain only
@@||fonts.google.com^
# Allows: fonts.google.com, but requires separate rule for other Google services
```

## Wildcard Patterns

### Keyword-Based Blocking
```bash
# Block any domain containing "ads"
||*ads*.com^
# Blocks: googleads.com, facebookads.com, popupads.com

# Block any domain containing "tracking"
||*tracking*.net^
# Blocks: analytics-tracking.net, user-tracking.net, clicktracking.net

# Block any domain containing "analytics"
||*analytics*^
# Blocks: google-analytics.com, site-analytics.org, analytics.facebook.com
```

### Pattern-Based Blocking
```bash
# Block crypto mining domains
||*mine*^
# Blocks: coinhive.com, cryptomine.com, jsmine.org

# Block gambling-related domains
||*bet*.com^
# Blocks: sportbet.com, casinobet.com, onlinebet.com

# Block adult content patterns
||*porn*^
# Blocks: pornhub.com, freeporn.com, mobileporn.net
```

### Advanced Wildcards
```bash
# Block social media ad domains
||*ads*.facebook.*^
# Blocks: ads.facebook.com, staticads.facebook.net

# Block tracker subdomains
||*tracker*.*^
# Blocks: tracker.site.com, analytics-tracker.example.net

# Block CDN advertising
||*ad-*.cloudfront.net^
# Blocks: ad-delivery.cloudfront.net, ad-serve.cloudfront.net
```

## Path-Specific Filtering

### Block Specific Paths
```bash
# Block Facebook tracking pixel
||facebook.com/tr/
# Blocks: facebook.com/tr/*, but allows facebook.com/

# Block YouTube ads
||youtube.com/ads/
# Blocks: youtube.com/ads/*, but allows youtube.com/watch

# Block Google Analytics tracking
||google-analytics.com/collect
# Blocks: google-analytics.com/collect, but allows other GA features

# Block specific ad endpoints
||doubleclick.net/gampad/ads
# Blocks: doubleclick.net/gampad/ads*, but allows other Doubleclick services
```

### Allow Specific Paths
```bash
# Allow only YouTube Kids
@@||youtube.com/kids/
# Allows: youtube.com/kids/*, but requires separate rule for main YouTube

# Allow specific Google services
@@||accounts.google.com/
# Allows: accounts.google.com/*, essential for Google sign-in

# Allow educational content only
@@||khanacademy.org/learn/
# Allows: khanacademy.org/learn/*, but blocks other sections if main domain blocked
```

## Real-World Examples

### Family-Safe Social Media
```bash
# Block main social platforms but allow specific safe areas
||facebook.com^
||instagram.com^
||tiktok.com^

# Allow specific educational/safe social content
@@||facebook.com/educational
@@||instagram.com/nationalgeographic
@@||youtube.com/kids
```

### Smart TV Ad Blocking
```bash
# Block Samsung TV ads and tracking
||samsungads.com^
||*samsung*ads*^
||*analytics*.samsung.com^

# Block LG TV ads
||lgads.tv^
||*ad*.lg.com^

# Block Roku advertising
||*ads*.roku.com^
||*tracking*.roku.com^
```

### Gaming Platform Filtering
```bash
# Block inappropriate gaming content
||*adult*.steam*^
||*mature*.gaming*^
||*18plus*.game*^

# Allow educational gaming
@@||scratch.mit.edu^
@@||code.org^
@@||minecraft.education^
```

### Educational Content Priority
```bash
# Block entertainment but allow educational
||*entertainment*^
||*games*^
||*fun*^

# Allow educational exceptions
@@||*educational*.com^
@@||*learning*.org^
@@||*academy*.edu^
@@||*school*.net^
```

## Advanced Filtering Strategies

### Cryptocurrency and Financial Blocking
```bash
# Block cryptocurrency services
||*crypto*^
||*bitcoin*^
||*blockchain*^
||*mining*^

# Block trading platforms
||*trade*^
||*trading*^
||*invest*^

# Allow specific educational finance content
@@||investopedia.com^
@@||khanacademy.org/economics-finance-domain/
```

### Privacy and Tracking Protection
```bash
# Block comprehensive tracking
||*track*^
||*analytic*^
||*metric*^
||*pixel*^
||*beacon*^

# Block data collection
||*collect*^
||*gather*^
||*harvest*^

# Block fingerprinting
||*fingerprint*^
||*identify*^
```

### Malware and Security
```bash
# Block malware distribution
||*malware*^
||*virus*^
||*trojan*^
||*ransomware*^

# Block phishing patterns
||*phishing*^
||*fake*.*security*^
||*urgent*.*update*^
||*verify*.*account*^

# Block suspicious TLDs
||*.tk^
||*.ml^
||*.ga^
||*.cf^
```

## Combining Patterns

### Layered Protection
```bash
# Base protection (everyone)
||*ads*^
||*tracking*^
||*analytics*^

# Child protection (additional)
||*adult*^
||*mature*^
||*dating*^
||*gambling*^

# Privacy protection (additional)
||*facebook*.com/tr
||*google*.com/collect
||*doubleclick*^
```

### Exception Handling
```bash
# Block broad category
||*social*^

# Allow specific exceptions
@@||socialmediamarketing.edu^
@@||socialstudies.org^
@@||antisocial.github.io^
```

## Testing and Validation

### Test Your Patterns
```bash
# Query specific domain in Pi-hole
pihole -q example.com

# Test wildcard effectiveness
pihole -q ads.example.com
pihole -q tracking.example.com

# Check allowlist overrides
pihole -q allowed.example.com
```

### Pattern Testing Examples
```bash
# Test ad blocking pattern
||*ads*.com^
# Should block: googleads.com, facebookads.com
# Should NOT block: example.com, google.com

# Test allowlist override
||social*^           # Blocks social networks
@@||socialstudies.org^   # Allows educational exception
```

## Common Mistakes to Avoid

### ❌ Incorrect Syntax
```bash
# Wrong - missing anchors
*ads*.com

# Wrong - regex instead of ABP
(^|\.)ads\..*\.com$

# Wrong - missing separator
||domain.com

# Correct - proper ABP syntax
||*ads*.com^
```

### ❌ Overly Broad Patterns
```bash
# Too broad - blocks legitimate sites
||*mail*^
# Blocks: gmail.com, email.com, mailchimp.com

# Better - more specific
||*spam*mail*^
||*junk*mail*^
```

### ❌ Missing Exceptions
```bash
# Blocks too much
||*ad*^
# Blocks: ads.com BUT ALSO address.com, canada.gov

# Add necessary exceptions
@@||address.com^
@@||canada.gov^
```

## Performance Tips

### Efficient Pattern Design
```bash
# Good - specific and efficient
||doubleclick.net^
||googleadservices.com^

# Less efficient - very broad wildcard
||*.*^

# Good compromise - targeted wildcards
||*ads*.com^
||*tracking*.net^
```

### List Organization
1. **Start specific** - exact domains first
2. **Add wildcards** - for pattern matching
3. **Include exceptions** - allowlist overrides
4. **Test thoroughly** - verify patterns work as expected

## Debugging ABP Lists

### Check Gravity Output
```bash
# Look for successful ABP parsing
pihole -g | grep -i "abp"

# Expected output:
# [✓] Parsed 0 exact domains and 150+ ABP-style domains
```

### Validate Syntax
```bash
# Check for regex patterns (should be converted)
grep -E '\(\^\|\\\.\).*\$$' your-list.txt

# Check for proper ABP format
grep -E '^(@@)?\|\|.*\^$' your-list.txt
```

### Test Pattern Matching
```bash
# Test specific domain against your patterns
echo "||*ads*.com^" | grep -E "ads\.example\.com"

# Validate allowlist overrides
pihole -q blocked-but-allowed.com
```

## Migration from Other Formats

### From Plain Domain Lists
```bash
# Old format
domain.com
subdomain.domain.com

# New ABP format
||domain.com^          # Covers all subdomains automatically
```

### From Regex Patterns
```bash
# Old regex format
(^|\.)domain\.com$

# New ABP format
||domain.com^
```

### From Hosts Files
```bash
# Old hosts format
0.0.0.0 domain.com
127.0.0.1 ads.domain.com

# New ABP format
||domain.com^
||ads.domain.com^
```

---

## Quick Reference Summary

| Pattern | Purpose | Example | Matches |
|---------|---------|---------|---------|
| `||domain.com^` | Block exact domain + subs | `||google.com^` | google.com, mail.google.com |
| `@@||domain.com^` | Allow exact domain + subs | `@@||github.com^` | github.com, api.github.com |
| `||*keyword*^` | Block domains with keyword | `||*ads*^` | googleads.com, facebookads.com |
| `||domain.com/path` | Block specific path | `||site.com/ads/` | site.com/ads/* only |
| `@@||domain.com/path` | Allow specific path | `@@||youtube.com/kids` | youtube.com/kids/* only |

**Remember:** ABP patterns are processed in order, with allowlist entries (`@@`) taking precedence over blocklist entries.