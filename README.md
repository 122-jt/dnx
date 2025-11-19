# DNX: Domain Explorer

[![Perl](https://img.shields.io/badge/Perl-5.10%2B-39457E?style=flat&logo=perl)](https://www.perl.org/)
[![License](https://img.shields.io/badge/License-MIT-00A98F?style=flat)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.0.0-FF6B6B?style=flat)](dnx)
[![Security](https://img.shields.io/badge/Security-Penetration%20Testing-red?style=flat&logo=hackaday)](dnx)

> Production-ready subdomain enumeration for security researchers. Fast, intelligent, and respectful of rate limits.

## Why DNX?

Stop cobbling together multiple tools. DNX combines passive reconnaissance with active brute-forcing in one clean package. Written in Perl for speed and portability.

**7 API sources + parallel DNS resolution + wildcard detection = Complete subdomain discovery**

## Quick Start
```bash
# Install dependencies and run
make deps
./dnx --domain target.com

# Full scan with wordlist
./dnx -d target.com --wordlist subdomains.txt --threads 50

# Passive only (zero packets to target)
./dnx -d target.com --passive
```

## Installation

### One-Line Install
```bash
make deps && sudo make install
```

### Manual Setup
```bash
# Install Perl modules
cpanm LWP::UserAgent JSON Net::DNS Term::ANSIColor

# Make executable
chmod +x dnx

# Optional: Install system-wide
sudo cp dnx /usr/local/bin/
```

### Docker (Coming Soon)
```bash
docker pull blackstack/dnx
docker run --rm blackstack/dnx -d target.com
```

## Features

**Passive Enumeration** • Query 7 sources without touching the target  
**Active Brute-Force** • Parallel DNS resolution with custom wordlists  
**Wildcard Detection** • Automatically filter false positives  
**Smart Caching** • 24-hour cache reduces API load  
**Multi-Format Output** • Text, JSON, CSV exports  
**Progress Tracking** • Real-time progress bars and ETA  
**Graceful Exit** • Ctrl+C saves partial results  
**DNS Rotation** • Distribute queries across multiple resolvers  
**Record Type Support** • A, AAAA, CNAME, MX, NS, TXT, SOA

## Data Sources

### Free APIs (No Key Required)

**crt.sh** • Certificate Transparency logs  
**HackerTarget** • DNS and threat intelligence  
**ThreatCrowd** • Crowdsourced threat data  
**AlienVault OTX** • Open Threat Exchange  
**Riddler.io** • Comprehensive DNS database

### Premium APIs (Optional)

**VirusTotal** • Enhanced subdomain discovery  
**Shodan** • Internet-wide scanning data

## Usage

### Basic Scans
```bash
# Quick passive scan
./dnx -d github.com

# Passive + brute-force
./dnx -d stripe.com --wordlist subdomains.txt

# High-speed mode (50 threads)
./dnx -d target.com -w large-list.txt --threads 50

# Save results
./dnx -d target.com -o results.json --format json
```

### Advanced Techniques
```bash
# Custom DNS resolvers
./dnx -d target.com --resolvers 1.1.1.1,8.8.8.8,9.9.9.9

# Multiple record types
./dnx -d target.com --record-types A,AAAA,MX,NS,TXT

# Slow and stealthy
./dnx -d target.com --threads 5 --rate-limit 3

# With API keys for maximum coverage
./dnx -d target.com \
  --shodan-key YOUR_SHODAN_KEY \
  --virustotal-key YOUR_VT_KEY

# Quiet mode for scripting
./dnx -d target.com -q -o subdomains.txt

# Verbose debugging
./dnx -d target.com -v --wordlist test.txt
```

### Input Flexibility

DNX handles various input formats automatically:
```bash
# All of these work:
./dnx -d example.com
./dnx -d https://example.com
./dnx -d http://example.com/path
./dnx -d example.com:443
```

## Command-Line Options

| Option | Description | Default |
|--------|-------------|---------|
| `-d, --domain` | Target domain (required) | - |
| `-w, --wordlist` | Brute-force wordlist | - |
| `-t, --threads` | Concurrent threads | 20 |
| `--timeout` | DNS timeout (seconds) | 5 |
| `-o, --output` | Output file | stdout |
| `-f, --format` | text, json, csv | text |
| `-p, --passive` | Passive only (no DNS) | false |
| `--no-bruteforce` | Skip brute-force | false |
| `-q, --quiet` | Minimal output | false |
| `-v, --verbose` | Detailed logging | false |
| `--resolvers` | DNS servers (comma-separated) | 8.8.8.8,1.1.1.1 |
| `--rate-limit` | API requests/second | 10 |
| `--record-types` | DNS types to query | A |
| `--shodan-key` | Shodan API key | - |
| `--virustotal-key` | VirusTotal API key | - |
| `-h, --help` | Show help | - |

## Configuration File

Create `~/.dnx/config.ini` for persistent settings:
```ini
# Performance
threads = 30
timeout = 5
rate_limit = 10

# Output
format = text

# DNS Resolvers
resolvers = 8.8.8.8,1.1.1.1,9.9.9.9

# API Keys (optional)
shodan_api_key = YOUR_KEY_HERE
virustotal_api_key = YOUR_KEY_HERE
```

## API Keys Setup

### VirusTotal

1. Sign up: https://www.virustotal.com/
2. Get key: https://www.virustotal.com/gui/my-apikey
3. Use it:
```bash
export VIRUSTOTAL_API_KEY="your_key"
./dnx -d target.com --virustotal-key "your_key"
```

### Shodan

1. Sign up: https://account.shodan.io/register
2. Get key: https://account.shodan.io/
3. Use it:
```bash
export SHODAN_API_KEY="your_key"
./dnx -d target.com --shodan-key "your_key"
```

## Output Formats

### Text (Default)

Clean, colorized terminal output:
```
www.github.com                                     140.82.121.4
api.github.com                                     140.82.121.6
docs.github.com                                    185.199.108.153
```

### JSON

Structured data for automation:
```json
{
  "domain": "github.com",
  "timestamp": "2025-11-19 12:34:56",
  "count": 157,
  "subdomains": [
    {
      "subdomain": "www.github.com",
      "ip": "140.82.121.4"
    }
  ]
}
```

### CSV

Import into spreadsheets:
```csv
subdomain,ip
www.github.com,140.82.121.4
api.github.com,140.82.121.6
```

## Performance

### Benchmarks

**Passive Scan:** 5-15 seconds (network dependent)  
**1K wordlist @ 20 threads:** 30-60 seconds  
**10K wordlist @ 50 threads:** 5-10 minutes  
**100K wordlist @ 100 threads:** 30-45 minutes

### Optimization Tips
```bash
# Maximum speed (use with caution)
./dnx -d target.com -w huge.txt --threads 100 --timeout 3

# Balanced approach
./dnx -d target.com -w medium.txt --threads 30 --timeout 5

# Stealth mode
./dnx -d target.com -w small.txt --threads 5 --rate-limit 3
```

**Pro Tips:**
- Start with passive enumeration
- Use targeted wordlists (quality > quantity)
- Increase threads only if your network can handle it
- Custom resolvers can dramatically improve speed
- Cache is your friend (24-hour retention)

## DNS Record Types

Query multiple record types simultaneously:
```bash
./dnx -d target.com --record-types A,AAAA,MX,NS,TXT
```

| Type | Purpose |
|------|---------|
| **A** | IPv4 addresses |
| **AAAA** | IPv6 addresses |
| **CNAME** | Canonical name aliases |
| **MX** | Mail exchange servers |
| **NS** | Name servers |
| **TXT** | Text records (SPF, DKIM, etc.) |
| **SOA** | Start of authority |

## Real-World Examples

### Bug Bounty Reconnaissance
```bash
# Full enumeration with all sources
./dnx -d target.com \
  --wordlist /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
  --threads 50 \
  --record-types A,AAAA,CNAME \
  --output target-subdomains.json \
  --format json
```

### Red Team Engagement
```bash
# Passive only (zero footprint)
./dnx -d target.com --passive -o recon.txt

# Review results, then targeted brute-force
./dnx -d target.com -w custom-wordlist.txt --threads 10
```

### Continuous Monitoring
```bash
# Daily cron job
0 2 * * * /usr/local/bin/dnx -d mycompany.com -q -o /var/log/subdomains-$(date +\%Y\%m\%d).json -f json

# Compare changes
diff /var/log/subdomains-20250119.json /var/log/subdomains-20250120.json
```

### Mass Scanning
```bash
# Scan multiple domains
for domain in $(cat domains.txt); do
  ./dnx -d $domain -p -o results/$domain.json -f json
  sleep 5
done
```

## Wordlists

DNX includes a basic wordlist. For comprehensive scanning:

**SecLists (Recommended)**
```bash
git clone https://github.com/danielmiessler/SecLists.git
./dnx -d target.com -w SecLists/Discovery/DNS/subdomains-top1million-110000.txt
```

**Other Quality Lists:**
- https://github.com/rbsec/dnscan
- https://gist.github.com/jhaddix/86a06c5dc309d08580a018c66354a056
- https://github.com/danielmiessler/SecLists/tree/master/Discovery/DNS

## Smart Features

### Progress Tracking

Real-time feedback for long scans:
```
[*] Passive Enumeration
    Querying APIs: 5/7 (71%)
    Found: 23 subdomains

[*] Active Brute-Force
    Progress: [████████░░░░░░░░] 45% (4500/10000)
    Found: 157 subdomains
```

### Graceful Exit

Press `Ctrl+C` anytime:
- Saves all discovered subdomains
- Shows elapsed time and statistics
- Cleans up temporary files
- No data loss

### Wildcard Detection

Automatically filters false positives:
```
[!] Wildcard DNS detected for *.target.com → 1.2.3.4
[*] Filtering wildcard responses...
```

## Makefile Commands
```bash
make              # Syntax check
make deps         # Install dependencies
make install      # Install to /usr/local/bin
make uninstall    # Remove from system
make test         # Run tests
make clean        # Clean cache and outputs
make help         # Show all commands
```

## Troubleshooting

### Missing Dependencies
```bash
# Error: Cannot locate Net/DNS.pm
cpanm Net::DNS

# Or install all at once
make deps
```

### Slow Performance
```bash
# Use faster DNS resolvers
./dnx -d target.com --resolvers 1.1.1.1,8.8.8.8

# Increase threads
./dnx -d target.com --threads 50

# Reduce timeout for dead hosts
./dnx -d target.com --timeout 3
```

### API Rate Limits
```bash
# Reduce request rate
./dnx -d target.com --rate-limit 5

# Use API keys for higher limits
./dnx -d target.com --virustotal-key YOUR_KEY
```

### Too Many False Positives
```bash
# Verbose mode shows wildcard detection
./dnx -d target.com -v

# Manual wildcard check
dig randomstring123.target.com
```

## Integration

### Pipe to Other Tools
```bash
# Feed to httpx for live host detection
./dnx -d target.com -q | httpx -silent

# Check with nuclei
./dnx -d target.com -q | nuclei -t vulnerabilities/

# Resolve with massdns
./dnx -d target.com -q > subs.txt
massdns -r resolvers.txt -t A -o S subs.txt
```

### JSON Processing
```bash
# Extract subdomains with jq
./dnx -d target.com -f json | jq -r '.subdomains[].subdomain'

# Filter by IP range
./dnx -d target.com -f json | jq '.subdomains[] | select(.ip | startswith("192.168"))'
```

## Security & Ethics

### Responsible Use

**DO:**
- Scan your own domains
- Get written permission before testing
- Respect rate limits and ToS
- Use for authorized security assessments

**DON'T:**
- Scan without permission
- Abuse public APIs
- Ignore rate limits
- Use for malicious purposes

### Legal Notice

This tool is for **authorized security testing only**. Users are responsible for complying with all applicable laws. Unauthorized scanning may be illegal in your jurisdiction.

## Contributing

Pull requests welcome for:

- Additional passive sources
- AXFR zone transfer attempts
- IPv6 improvements
- Database export (SQLite, PostgreSQL)
- Integration with OSINT frameworks

## Comparison

| Feature | DNX | Sublist3r | Amass | Subfinder |
|---------|-----|-----------|-------|-----------|
| Speed | Fast | Slow | Slow | Very Fast |
| API Sources | 7 | 8 | 55+ | 40+ |
| Threading | Yes | Limited | Yes | Yes |
| Caching | 24h | No | Yes | Yes |
| Wildcard Detection | Yes | No | Yes | Yes |
| Zero Dependencies | Perl stdlib | Python deps | Go binary | Go binary |
| Output Formats | 3 | 1 | 5 | 4 |

## Roadmap

- [ ] AXFR zone transfer detection
- [ ] Certificate transparency stream monitoring
- [ ] Subdomain takeover detection
- [ ] Integration with Metasploit/Cobalt Strike
- [ ] Web interface
- [ ] Machine learning for wordlist optimization

## Credits

**Author:** bl4ckstack  
**License:** MIT  
**Version:** 1.0.0

**Built for security researchers who value speed, accuracy, and respect for infrastructure.**

---

**Star it if DNX helped you find something interesting** • Contributions welcome!
