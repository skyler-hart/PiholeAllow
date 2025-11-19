# Quick Start Guide

## Which List Should I Use?

### Scenario 1: Children's Devices (iPad, kids' computer)
**Use:** `kids-only.txt`
- Includes: Family-wide sites + Kids-specific content
- Blocks: Adult-only services (financial, work tools, etc.)
- **URL:** `https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/kids-only.txt`

### Scenario 2: Parent Devices (laptop, phone)
**Use:** `adults-only.txt`
- Includes: Family-wide sites + Adult services
- Does not include: Kids-specific game/entertainment sites
- **URL:** `https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/adults-only.txt`

### Scenario 3: Shared Family Devices (living room TV, shared tablet)
**Use:** `all-family.txt`
- Includes: Everything (family + kids + adults)
- **URL:** `https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/all-family.txt`

## Step-by-Step Pi-hole Setup

### For Pi-hole with Web Interface

1. **Access your Pi-hole admin panel**
   - Open browser: `http://pi.hole/admin` or `http://YOUR_PI_IP/admin`
   - Log in with your password

2. **Navigate to Allowlist**
   - Click **Group Management** in the left sidebar
   - Click **Adlists**

3. **Add your chosen list**
   - Copy one of the URLs above
   - Paste into the "Address" field
   - Add a comment (e.g., "Family Allow List - Kids")
   - Click **Add**

4. **Update Gravity**
   - Go to **Tools** → **Update Gravity**
   - Click **Update**
   - Wait for the process to complete

5. **Verify**
   - Go to **Dashboard**
   - Check that domains are being allowed

### For Pi-hole via Command Line

```bash
# SSH into your Pi-hole server
ssh pi@YOUR_PI_IP

# Download the list you want
wget https://raw.githubusercontent.com/skyler-hart/PiholeAllow/main/lists/kids-only.txt -O /tmp/allow.txt

# Add each domain to Pi-hole
while read domain; do
  # Skip comments and empty lines
  [[ "$domain" =~ ^#.*$ ]] && continue
  [[ -z "$domain" ]] && continue
  pihole -w "$domain"
done < /tmp/allow.txt

# Update gravity
pihole -g

# Verify
pihole -q google.com
```

## Advanced Setup: Using Groups

For families with multiple devices needing different access levels:

### Step 1: Create Groups
```bash
# Access your Pi-hole admin panel
# Go to Group Management → Groups
# Create groups: Kids, Adults, Everyone
```

### Step 2: Assign Devices to Groups
```bash
# Go to Group Management → Clients
# Add each device's IP or MAC address
# Assign to appropriate group
```

### Step 3: Add Lists to Groups
```bash
# Go to Group Management → Adlists
# For each list, select which groups can access it
```

### Example Configuration:
- **Kids' iPad** → Kids group → `kids-only.txt`
- **Mom's laptop** → Adults group → `adults-only.txt`
- **Dad's phone** → Adults group → `adults-only.txt`
- **Living room TV** → Everyone group → `all-family.txt`

## Customization

### Adding Your Own Domains

1. **Fork this repository** (click Fork button on GitHub)

2. **Edit the appropriate file:**
   - `lists/everyone.txt` - for family-wide domains
   - `lists/kids.txt` - for kid-specific domains
   - `lists/adults.txt` - for adult-specific domains

3. **Add your domain** (one per line):
   ```
   example.com
   another-site.com
   ```

4. **Commit and push** your changes

5. **Update your Pi-hole URL** to use your fork:
   ```
   https://raw.githubusercontent.com/YOUR_USERNAME/PiholeAllow/main/lists/kids-only.txt
   ```

### Removing Domains

Edit the list files and delete the lines you don't want, then update Gravity in Pi-hole.

## Troubleshooting

### Domain Still Blocked
1. Check if the domain is in your blocklist: `pihole -q example.com`
2. Add manually: `pihole -w example.com`
3. Update gravity: `pihole -g`

### Too Many Domains Allowed
1. Review your allow list
2. Remove unwanted domains
3. Consider using a more restrictive list (e.g., switch from `all-family.txt` to `kids-only.txt`)

### Lists Not Updating
1. Check your internet connection
2. Verify the GitHub URL is correct
3. Try updating manually: `pihole -g`
4. Check Pi-hole logs: `/var/log/pihole.log`

## Maintenance

### Keeping Lists Updated

Pi-hole automatically updates lists weekly. To manually update:

**Web Interface:**
1. Go to Tools → Update Gravity
2. Click Update

**Command Line:**
```bash
pihole -g
```

### Checking What's Allowed

```bash
# Query a specific domain
pihole -q example.com

# View all allowed domains
pihole -w -l
```

## Example Use Cases

### Case 1: Homework Mode
Create a temporary strict list for homework time by using only `everyone.txt` without kids games:
```bash
# Remove kids gaming sites temporarily
pihole -b roblox.com minecraft.net
# Add them back later
pihole -w roblox.com minecraft.net
```

### Case 2: Bedtime Restrictions
Use Pi-hole groups with time-based filtering (requires additional setup) to restrict access after bedtime.

### Case 3: Vacation Mode
Switch to `all-family.txt` for relaxed filtering during holidays, then back to stricter lists for school days.

## Support

If you encounter issues:
1. Check Pi-hole documentation: https://docs.pi-hole.net/
2. Review this guide
3. Open an issue on GitHub
4. Check Pi-hole community forums

## Best Practices

1. **Test before full deployment** - Try lists on one device first
2. **Keep lists updated** - Pull latest changes regularly
3. **Customize for your family** - Fork and modify lists as needed
4. **Monitor usage** - Review Pi-hole logs to see what's being blocked/allowed
5. **Communicate with family** - Let everyone know about filtering rules
6. **Regular reviews** - Update lists as kids grow and needs change
