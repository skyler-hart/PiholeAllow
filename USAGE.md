# Pi-hole Allow/Block Lists - Usage Guide

## Overview

This repository provides curated **ABP-style** (Adblock Plus format) lists for Pi-hole to manage family internet access. All lists use the modern `||domain.com^` and `@@||domain.com^` syntax for better compatibility and wildcard support.

## File Structure

### 📁 **Whitelists (Allow Lists)**
- `whitelists/everyone.txt` - Family-wide allowed domains (all family members)
- `whitelists/adults.txt` - Adult-only allowed domains (parents/guardians)  
- `whitelists/kids.txt` - Kid-specific allowed domains (educational/safe content)

### 🚫 **Blocklists**
- `blocklists/everyone.txt` - Family-wide blocked domains (harmful content for all)
- `blocklists/kids.txt` - Additional blocks for children (social media, mature content)
- `blocklists/dns.txt` - DNS bypass prevention (DoH, private relay, smart TV hardcoded DNS)

## ABP-Style Format

All lists now use **ABP (Adblock Plus) syntax** for better Pi-hole compatibility:

### **Allowlist Format:**
```
# Allow domain and all subdomains
@@||google.com^
@@||youtube.com^
@@||khanacademy.org^
```

### **Blocklist Format:**
```
# Block domain and all subdomains
||pornhub.com^
||gambling-site.com^

# Block with wildcards
||*ads*.com^
||*tracking*.net^
```

### **Benefits of ABP Format:**
- ✅ **Subdomain coverage** - Automatically handles `www.domain.com`, `mail.domain.com`, etc.
- ✅ **Wildcard support** - Use `*` for pattern matching
- ✅ **Override capability** - Allowlists take precedence over blocklists
- ✅ **Pi-hole native** - Parsed as "ABP-style domains" instead of "non-domain entries"

## Recommended Setup Scenarios

### 🧒 **Scenario 1: Children's Devices** (iPad, kids' computer)
**Recommended Lists:**
- **Allowlist:** `whitelists/everyone.txt` + `whitelists/kids.txt`
- **Blocklist:** `blocklists/everyone.txt` + `blocklists/kids.txt` + `blocklists/dns.txt`
- **Result:** Educational content + family-safe sites, blocks adult content + social media + DNS bypass

**URLs:**
```
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/everyone.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/kids.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/everyone.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/kids.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/dns.txt
```

### 👨‍👩‍👧‍👦 **Scenario 2: Parent Devices** (laptop, phone)
**Recommended Lists:**
- **Allowlist:** `whitelists/everyone.txt` + `whitelists/adults.txt`
- **Blocklist:** `blocklists/everyone.txt` + `blocklists/dns.txt`
- **Result:** Full adult access including work/financial services, blocks harmful content + DNS bypass

**URLs:**
```
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/everyone.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/adults.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/everyone.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/dns.txt
```

### 📺 **Scenario 3: Shared Family Devices** (living room TV, shared tablet)
**Recommended Lists:**
- **Allowlist:** `whitelists/everyone.txt` only
- **Blocklist:** `blocklists/everyone.txt` + `blocklists/dns.txt`
- **Result:** Family-friendly content for all ages, blocks harmful content + smart TV DNS bypass

**URLs:**
```
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/everyone.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/everyone.txt
https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/dns.txt
```

### 🔒 **Scenario 4: Maximum Security Setup**
**Recommended Lists:**
- **Allowlist:** `whitelists/everyone.txt` + specific user lists
- **Blocklist:** ALL lists (`blocklists/everyone.txt` + `blocklists/kids.txt` + `blocklists/dns.txt`)
- **Result:** Strictest filtering, prevents all bypasses, curated safe content only

## Step-by-Step Pi-hole Setup

### Method 1: Web Interface (Recommended)

#### Adding Allowlists
1. **Access Pi-hole admin panel:** `http://pi.hole/admin` or `http://YOUR_PI_IP/admin`
2. **Navigate:** Group Management → Adlists
3. **Add allowlist:** 
   - Paste allowlist URL in "Address" field
   - Comment: "Family Allow - Everyone" (or similar)
   - Click **Add**
4. **Repeat** for each allowlist you need

#### Adding Blocklists  
1. **Navigate:** Group Management → Adlists
2. **Add blocklist:**
   - Paste blocklist URL in "Address" field  
   - Comment: "Family Block - Everyone" (or similar)
   - Click **Add**
3. **Repeat** for each blocklist you need

#### Update and Verify
1. **Update Gravity:** Tools → Update Gravity → **Update**
2. **Check results:** Should show "X ABP-style domains" parsed
3. **Test:** Tools → Query Lists → Test a domain

### Method 2: Command Line

#### Using ABP Lists Directly
```bash
# Pi-hole now supports ABP lists natively via Adlists
# Add via web interface or sqlite database

# Add allowlist to Pi-hole
sqlite3 /etc/pihole/gravity.db "INSERT INTO adlist (address, comment) VALUES ('https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/everyone.txt', 'Family Allow - Everyone');"

# Add blocklist to Pi-hole  
sqlite3 /etc/pihole/gravity.db "INSERT INTO adlist (address, comment) VALUES ('https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/everyone.txt', 'Family Block - Everyone');"

# Update gravity to process ABP lists
pihole -g

# Verify ABP parsing
tail /var/log/pihole.log | grep "ABP-style"
```

#### Legacy Method (Manual Domain Addition)
```bash
# Only use if ABP lists don't work with your Pi-hole version
# Download and process allowlist
curl -s https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/whitelists/everyone.txt | \
  grep -E '^@@\|\|.*\^$' | \
  sed 's/^@@||//;s/\^$//' | \
  while read domain; do pihole -w "$domain"; done

# Download and process blocklist  
curl -s https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/blocklists/everyone.txt | \
  grep -E '^\|\|.*\^$' | \
  sed 's/^||//;s/\^$//' | \
  while read domain; do pihole -b "$domain"; done
```

## Advanced Setup: Using Pi-hole Groups

For families with multiple devices needing different access levels:

### Step 1: Create Groups
1. **Web Interface:** Group Management → Groups
2. **Create groups:** 
   - `Kids` (children's devices)
   - `Adults` (parent devices)  
   - `Family` (shared devices)

### Step 2: Assign Devices to Groups
1. **Web Interface:** Group Management → Clients
2. **Add each device:**
   - Enter IP address or MAC address
   - Assign to appropriate group(s)
   - Enable/disable as needed

### Step 3: Assign Lists to Groups
1. **Web Interface:** Group Management → Adlists  
2. **For each list, select target groups:**
   - Everyone allowlist → All groups
   - Kids allowlist → Kids group only
   - Adults allowlist → Adults group only
   - DNS blocklist → All groups

### Example Multi-Group Configuration:

| Device | Groups | Allowlists | Blocklists |
|--------|---------|------------|------------|
| **Kids' iPad** | `Kids` | `everyone.txt` + `kids.txt` | `everyone.txt` + `kids.txt` + `dns.txt` |
| **Mom's laptop** | `Adults` | `everyone.txt` + `adults.txt` | `everyone.txt` + `dns.txt` |
| **Dad's phone** | `Adults` | `everyone.txt` + `adults.txt` | `everyone.txt` + `dns.txt` |
| **Living room TV** | `Family` | `everyone.txt` | `everyone.txt` + `dns.txt` |

## Customization

### Forking and Customizing Lists

1. **Fork this repository** (click Fork button on GitHub)

2. **Edit files for your needs:**
   - `whitelists/everyone.txt` - Add family-wide domains
   - `whitelists/kids.txt` - Add educational sites
   - `whitelists/adults.txt` - Add work/professional domains
   - `blocklists/everyone.txt` - Add harmful domains to block
   - `blocklists/kids.txt` - Add age-inappropriate content

3. **Use ABP syntax:**
   ```
   # Allowlist entries
   @@||my-custom-site.com^
   @@||educational-game.org^
   
   # Blocklist entries  
   ||bad-site.com^
   ||*ads*.unwanted-domain.net^
   ```

4. **Update your Pi-hole URLs** to use your fork:
   ```
   https://raw.githubusercontent.com/YOUR_USERNAME/PiholeAllow/main/whitelists/everyone.txt
   ```

### Adding Your Own Domains

**For Allowlists (whitelists):**
```
@@||my-work-site.com^
@@||family-blog.net^  
@@||kids-educational-app.org^
```

**For Blocklists:**
```
||unwanted-site.com^
||*tracking*.badcompany.net^
||social-media-distraction.com^
```

### Understanding ABP Wildcards

```
# Exact domain and subdomains
@@||example.com^          # Allows example.com, www.example.com, api.example.com

# Wildcard patterns
||*ads*.com^              # Blocks any domain containing "ads" 
||*tracking*.net^         # Blocks tracking-related domains
@@||*educational*.org^    # Allows educational domains
```

## Troubleshooting

### Expected Gravity Output
When lists are working correctly, you should see:
```
[✓] Parsed 0 exact domains and 150+ ABP-style domains 
    (allowlisting/blocking, ignored 0 non-domain entries)
```

### Common Issues

#### "Non-domain entries" in Gravity
**Problem:** `ignored X non-domain entries`
**Cause:** Regex patterns `(^|\.)domain\.com$` instead of ABP format
**Solution:** Ensure all entries use ABP syntax:
- Allowlist: `@@||domain.com^`  
- Blocklist: `||domain.com^`

#### Domain Still Blocked
```bash
# Check domain status
pihole -q example.com

# Add manually to allowlist
pihole -w example.com

# Update gravity and test
pihole -g
pihole -q example.com
```

#### Lists Not Updating
1. **Check internet connection** on Pi-hole device
2. **Verify URLs** are correct and accessible
3. **Manual update:** Tools → Update Gravity
4. **Check logs:** `/var/log/pihole.log`

#### ABP Lists Not Parsing
1. **Update Pi-hole:** Ensure you have a recent version supporting ABP
2. **Check format:** Verify all entries use proper `@@||` or `||` syntax
3. **Test individual domains:** Add manually to verify functionality

### DNS Bypass Still Working
If devices are still bypassing Pi-hole despite `dns.txt` blocklist:
1. **Check if DNS blocklist is active** in Group Management → Adlists
2. **Verify device DNS settings** point to Pi-hole IP only
3. **Router configuration:** Set Pi-hole as DHCP DNS server
4. **Firewall rules:** Block outbound DNS (port 53) to non-Pi-hole IPs

### Smart TV Still Shows Ads
1. **Confirm `dns.txt` blocklist is enabled**
2. **Check TV network settings** - should use Pi-hole DNS only
3. **Router-level enforcement:** Block all DNS except Pi-hole
4. **TV-specific:** Some apps use hardcoded IPs, may need additional blocking

## Maintenance

### Automatic Updates
Pi-hole automatically updates lists weekly. **No manual action needed** for most users.

### Manual Updates
**Web Interface:** Tools → Update Gravity → **Update**

**Command Line:**
```bash
pihole -g
```

### Monitoring and Verification

#### Check What's Allowed/Blocked
```bash
# Query specific domain
pihole -q example.com

# View allowlist
pihole -w -l

# View blocklist  
pihole -b -l

# Check recent queries
pihole tail
```

#### Verify ABP Parsing
```bash
# Check gravity output for ABP domains
tail -f /var/log/pihole.log | grep -i "abp\|parsed"

# Should show: "Parsed X exact domains and Y ABP-style domains"
```

#### Monitor Family Usage
1. **Dashboard:** View query statistics and top domains
2. **Long-term data:** Analyze family browsing patterns
3. **Query log:** Real-time monitoring of requests

### Updating Your Fork
If you forked this repository:
```bash
# Keep your fork updated with latest changes
git remote add upstream https://github.com/skyler-hart/PiholeAllow.git
git fetch upstream  
git checkout main
git merge upstream/main
git push origin main
```

## Example Family Configurations

### 📚 **Education-First Family**
**Goal:** Prioritize learning, limit entertainment
```
Kids devices: everyone.txt + kids.txt + everyone-block.txt + kids-block.txt + dns.txt
Parent devices: everyone.txt + adults.txt + everyone-block.txt + dns.txt
Shared devices: everyone.txt + everyone-block.txt + dns.txt
```

### 🎮 **Balanced Family**  
**Goal:** Mix of education and entertainment
```
Kids devices: everyone.txt + kids.txt + everyone-block.txt + dns.txt
Parent devices: everyone.txt + adults.txt + everyone-block.txt + dns.txt
Gaming devices: everyone.txt + everyone-block.txt + dns.txt
```

### 🔒 **Security-Focused Family**
**Goal:** Maximum protection and privacy
```
All devices: everyone.txt + respective user lists + ALL blocklists
Router: Additional firewall rules blocking DNS bypass
Network: VPN + Pi-hole for double protection
```

### 👶 **Young Children Family**
**Goal:** Very restrictive, educational content only
```
Kids devices: kids.txt only + ALL blocklists
Parent devices: everyone.txt + adults.txt + everyone-block.txt + dns.txt
Shared devices: kids.txt + everyone-block.txt + kids-block.txt + dns.txt
```

## Support & Resources

### Getting Help
1. **Pi-hole Documentation:** https://docs.pi-hole.net/
2. **Pi-hole Community:** https://discourse.pi-hole.net/
3. **This Repository Issues:** https://github.com/skyler-hart/PiholeAllow/issues
4. **ABP Filter Syntax:** https://help.adblockplus.org/hc/en-us/articles/360062733293

### Quick Reference

#### File Purposes
| File | Purpose | Format | Target |
|------|---------|---------|---------|
| `whitelists/everyone.txt` | Family-safe domains for all | `@@\|\|domain.com^` | All family members |
| `whitelists/kids.txt` | Educational/kid-specific domains | `@@\|\|domain.com^` | Children only |
| `whitelists/adults.txt` | Work/adult-oriented domains | `@@\|\|domain.com^` | Parents/guardians |
| `blocklists/everyone.txt` | Harmful content for all | `\|\|domain.com^` | All family members |
| `blocklists/kids.txt` | Age-inappropriate content | `\|\|domain.com^` | Children's devices |
| `blocklists/dns.txt` | DNS bypass prevention | `\|\|domain.com^` | All devices |

#### ABP Syntax Quick Reference
```bash
# Allowlist (whitelist) entries
@@||domain.com^           # Allow domain and all subdomains
@@||specific.domain.com^  # Allow specific subdomain only
@@||domain.com/path       # Allow specific path

# Blocklist entries  
||domain.com^             # Block domain and all subdomains
||*keyword*.com^          # Block any domain containing "keyword"
||domain.com/ads/         # Block specific path only
```

### Best Practices Summary

1. ✅ **Start with everyone.txt** for all devices, then add user-specific lists
2. ✅ **Always include dns.txt** blocklist to prevent bypassing  
3. ✅ **Test on one device first** before deploying family-wide
4. ✅ **Use groups** for different family members with different needs
5. ✅ **Monitor Pi-hole logs** to catch blocked essential services
6. ✅ **Keep lists updated** by enabling automatic gravity updates
7. ✅ **Communicate with family** about filtering rules and exceptions
8. ✅ **Regular review** - adjust lists as children grow and needs change

### Version Requirements
- **Pi-hole:** v5.0+ recommended for full ABP support
- **Lists:** All lists are ABP-style format compatible
- **Legacy Pi-hole:** Use manual domain addition method if ABP parsing fails

---
