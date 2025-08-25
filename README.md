# hdns: Command-line interface for Hetzner DNS

`hdns` is a command-line interface for interacting with Hetzner DNS.

## WARNING

__DO NOT USE IN PRODUCTION__ - this script is only lightly tested!


## Motivation

I created `hdns` as a companion to the [hcloud](https://github.com/hetznercloud/cli) cli primarily for my personal Hetzner infrastructure automation needs that has minimal external dependencies available on the hosts I care for. An official Go-based version from Hetzner would obviously be preferable, but for whatever reason it does not exist (yet). 

## Overview

`hdns` is a Bash/curl/yq script that uses the Hetzner DNS API to manage DNS zones and records. It supports creating, updating, deleting, and exporting/importing DNS zones and records, as well as bulk operations and multiple output formats (JSON, TSV, YAML, table).

## Installation

### Prerequisites

- **Bash**: Version 4.4 or higher (I'm using 5.3).
- **curl**: For making API requests.
- **yq**: For parsing and formatting JSON/YAML responses (version 4.x recommended).


### Setup

1. **Get the `hdns` script and put it somewhere in `$PATH`**:

   Either clone the Github repository ...

   ```bash
   git clone https://github.com/<your-username>/hdns.git
   cd hdns
   cp /path/to/hdns
   ```

   or download it directly from the Github repository ...

   ```bash
   curl https://raw.githubusercontent.com/<your-username>/hdns/main/hdns >/path/to/hdns
   ```

2. **Make the Script Executable**:
   ```bash
   chmod +x /path/to/hdns
   ```

3. **Set Up API Token**:
   
   You can obtain your API token from the [Hetzner DNS Console](https://dns.hetzner.com/).

   `hdns` uses the HCLOUD_TOKEN environment variable if set, or searches in `~/.config/hdns/env`, `~/.env` and `$PWD/.env` - in this order. It does not source the whole file, but only extracts the HCLOUD_TOKEN line.

   Create a `~/.config/hdns/env` file with your Hetzner DNS API token:
   ```bash
   echo "HCLOUD_TOKEN=your-api-token-here" > ~/.config/hdns/env
   ```

## Usage

The `hdns` CLI currently provides two main resources: `zone` and `record`. Each resource has multiple commands, and help is available for each.

`hdns --help` shows extended help with examples.

```bash
$ ./hdns --help
A command-line interface for Hetzner DNS

Usage:
    hdns [command]

Resources:
    zone list                                    List all DNS zones
    zone get <zone_id|zone_name>                 Get DNS zone details
    zone describe <zone_id|zone_name>            Describe DNS zone details (alias for get)
    zone create <domain> [ttl]                   Create new DNS zone
    zone update <zone_id> <zone_name> [ttl]      Update DNS zone
    zone delete <zone_id|zone_name>              Delete DNS zone
    zone export-file <zone_id|zone_name>         Export DNS zone in BIND zonefile format
    zone validate-file <file>                    Validate DNS zone file in BIND zonefile format
    zone import-file <zone_id|zone_name> <file>  Import DNS zone file in BIND zonefile format

    record list [zone_id|zone_name]              List all DNS records (optionally filtered by zone)
    record get <record_id>                       Get DNS record details
    record describe <record_id>                  Describe DNS record details (alias for get)
    record create <zone_id|zone_name> <type> <name> <value> [ttl]   Create DNS record
    record search <zone_id|zone_name> <search_string>   Search in DNS record details
    record update <record_id> <zone_id|zone_name> <type> <name> <value> [ttl]   Update DNS record
    record delete <record_id>                    Delete DNS record
    record create-bulk <file>                    Bulk create DNS records from JSON file
    record export-bulk <zone_id|zone_name>       Export DNS records as JSON suitable for update-bulk
    record update-bulk <file>                    Bulk update DNS records from JSON file

Flags:
    -h, --help                          Show this help

Use "hdns [command] --help" for more information about a command.


DNS Record Types:
    A, AAAA, NS, MX, CNAME, RP, TXT, SOA, HINFO, SRV, DANE, TLSA, DS, CAA

Examples:
    # Zone management
    hdns zone list
    hdns zone create example.com 3600
    hdns zone delete example.com

    # Zone file operations
    hdns zone export-file example.com > /path/to/zone.txt
    hdns zone validate-file /path/to/zone.txt
    hdns zone import-file example.com /path/to/zone.txt

    # Record management
    hdns record list example.com
    hdns record create example.com A www 192.168.1.1 3600
    hdns record create example.com CNAME blog www.example.com
    hdns record create example.com MX @ "10 mail.example.com"
    hdns record create example.com TXT @ "v=spf1 include:_spf.google.com ~all"

    # Record bulk operations
    hdns record export-bulk example.com > /path/to/updates.json
    hdns record update-bulk /path/to/updates.json
    hdns record create-bulk /path/to/records.json
```

## Environment Variables
- `HCLOUD_TOKEN`: Your Hetzner DNS API token (required). Set this in one of the env files or as an environment variable.

## Contributing
At the moment I have no time to look into outside contributions, but this might change at some time. I still hope for an official cli from Hetzner and do not intend to substantially extend this Bash version.

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Acknowledgments
- Built with Bash, thanks Chet Ramey.
- Uses the [Hetzner DNS API](https://dns.hetzner.com/api-docs) from the awesome folks at [Hetzner](https://www.hetzner.com)
- Depends on [curl](https://curl.se) and [yq](https://github.com/mikefarah/yq) for API interactions and output formatting.
