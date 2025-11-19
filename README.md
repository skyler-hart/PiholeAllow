# PiholeAllow

A family-friendly Pi-hole allow list system with different permission levels for household members.

## Overview

This repository contains curated allow lists for Pi-hole DNS servers, designed to provide different access levels for family members:
- **Kids**: Safe, age-appropriate content
- **Adults**: Professional and personal services for parents
- **Everyone**: Common family-wide services

## List Structure

### Individual Lists

Located in the `lists/` directory:

- **`everyone.txt`** - Core domains that all family members can access
  - Essential services (Google, Wikipedia, GitHub)
  - Email services (Gmail, Outlook)
  - Cloud storage (Google Drive, Dropbox)
  - Family-friendly streaming (Netflix, Disney+)
  - Educational sites (Khan Academy, Coursera)
  - Online shopping (Amazon, Target)

- **`kids.txt`** - Child-specific domains
  - Educational sites (PBS Kids, ABCya, Starfall)
  - Safe entertainment (Nick, Cartoon Network)
  - Kids games (Roblox, Minecraft)
  - Learning platforms (Scratch, Code.org)

- **`adults.txt`** - Adult-specific domains
  - Work tools (LinkedIn, Slack, Zoom)
  - Financial services (PayPal, banking sites)
  - News and media (Reddit, Medium, NYTimes)
  - Travel services (Expedia, Airbnb)
  - Professional development

### Combined Lists

Pre-merged lists for easier Pi-hole integration:

- **`all-family.txt`** - Complete list (Everyone + Kids + Adults)
- **`kids-only.txt`** - Everyone + Kids
- **`adults-only.txt`** - Everyone + Adults

## Usage

### Option 1: Use Pre-Combined Lists (Recommended)

These are the easiest to use and maintain:

1. **For children's devices**: Use `kids-only.txt`
   ```
   https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/kids-only.txt
   ```

2. **For adult devices**: Use `adults-only.txt`
   ```
   https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/adults-only.txt
   ```

3. **For unrestricted access**: Use `all-family.txt`
   ```
   https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/all-family.txt
   ```

### Option 2: Combine Individual Lists

For more granular control, combine the base lists:

1. **Kids access**: 
   - Add `everyone.txt` + `kids.txt`

2. **Adult access**:
   - Add `everyone.txt` + `adults.txt`

3. **Full family access**:
   - Add `everyone.txt` + `kids.txt` + `adults.txt`

## Pi-hole Integration

### Method 1: Allowlist Management (Recommended)

1. Log into your Pi-hole admin panel
2. Navigate to **Group Management** → **Adlists** (if using groups)
3. Or navigate to **Allowlist** → **Domains**
4. Add the raw GitHub URL(s) for your chosen list(s)
5. Update Gravity: `pihole -g`

### Method 2: Command Line

```bash
# Download the list
wget https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/adults-only.txt -O /tmp/allow.txt

# Add domains to Pi-hole
while read domain; do
  # Skip comments and empty lines
  [[ "$domain" =~ ^#.*$ ]] && continue
  [[ -z "$domain" ]] && continue
  pihole -w "$domain"
done < /tmp/allow.txt

# Update gravity
pihole -g
```

### Method 3: Using Pi-hole Groups (Advanced)

For households with multiple devices needing different access levels:

1. Create groups: `Kids`, `Adults`, `Everyone`
2. Assign devices to appropriate groups
3. Add lists to corresponding groups
4. This allows per-device filtering based on who uses that device

## Customization

### Adding Your Own Domains

1. Fork this repository
2. Edit the appropriate list file(s)
3. Add domains (one per line)
4. Commit and push your changes
5. Update your Pi-hole to use your forked repository URL

### File Format

- One domain per line
- Comments start with `#`
- No wildcards needed (Pi-hole handles subdomains)
- Example:
  ```
  # Social Media
  facebook.com
  instagram.com
  ```

## Maintenance

### Updating Lists

To pull the latest updates:

```bash
# In Pi-hole admin
pihole -g

# Or use the web interface:
# Tools → Update Gravity
```

### Contributing

Feel free to submit pull requests with:
- Additional family-friendly domains
- Better categorization
- Bug fixes or improvements

## License

This project is provided as-is for personal use. Domain lists are curated for general family use and should be reviewed and customized based on your family's specific needs.

## Disclaimer

These lists are starting points and should be customized for your family's needs. Always review and test the lists before deploying in production. Parents should monitor children's internet usage regardless of filtering solutions in place.