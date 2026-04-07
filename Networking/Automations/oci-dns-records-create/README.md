## DNS Automation in OCI Private Zones Using Python and Bash

Managing DNS records in Oracle Cloud Infrastructure (OCI) is simple for small environments. However, as cloud environments scale across microservices, hybrid architectures, and large onboarding pipelines, manual DNS management becomes inefficient and error-prone.

This project demonstrates how to automate DNS record management in OCI Private Zones using:

Bash + OCI CLI (fast bulk updates)
Python + OCI SDK (intelligent incremental updates)

## Problem Statement
In real-world environments:
  - DNS records are maintained in spreadsheets or CMDBs
  - Bulk onboarding requires multiple DNS entries
  - Manual updates via console are error-prone
  - Duplicate records and inconsistencies occur

## Requirements
  - Bulk processing
  - Input-driven automation (CSV / Excel)
  - Safe updates (avoid overwrites)
  - Repeatable execution

## Solution Architecture
  CSV / Excel → Script (Bash / Python) → OCI CLI / SDK → OCI Private DNS Zone
  Input Layer: CSV / Excel
  Processing Layer: Script validation & transformation
  Execution Layer: OCI CLI / SDK
  Output: DNS records created/updated

## Approach 1: Bash + OCI CLI

- How to Run (Bash Script)

  1. Create CSV file
  
  cat > records.csv <<EOF
  DOMAIN,TYPE,TTL,RDATA
  host1.abc.com,A,3600,10.0.0.10|10.0.0.11
  EOF
  
  2. Fix line endings 
  
  sed -i 's/\r$//' dns_records_update.sh
  
  3. Make script executable
  
  chmod +x dns_records_update.sh
  
  4. Update variables in script
  
  VIEW_ID="<PRIVATE_VIEW_OCID>"
  
  ZONE="<ZONE_NAME or OCID>"
  
  CSV_FILE="records.csv"
  
  5. Execute
  
  ./dns_records_update.sh

- Behavior
  1. Processes DNS records from a CSV input file
  2. Supports A, AAAA, and CNAME record types
  3. Handles multiple RDATA values using a delimiter (|)
  4. Dynamically constructs OCI CLI payloads
  5. Performs full RRSet updates (overwrite behavior)
  6. Executes one request per record set
- When to Use
  1. Bulk DNS record creation or updates
  2. CI/CD pipelines and automation scripts
  3. Scenarios where full overwrite is acceptable
  4. Quick, lightweight automation without complex logic

## Approach 2: Python + OCI SDK

- How to Run (Python Script)

  1. Install dependencies
  
    pip install oci pandas openpyxl
  
  2. Configure OCI CLI
  
    oci setup config
  
  3. Update script inputs
  
    ZONE_NAME = "<ZONE_OCID>"
  
    COMPARTMENT_ID = "<COMPARTMENT_OCID>"
  
    EXCEL_FILE = "dns_sample.xlsx"
  
  4. Run Script 
  
    python3 dns_records_update.py

- Behavior
  1. Reads DNS records from an Excel input file
  2. Retrieves existing DNS records from OCI
  3. Compares desired state with current DNS state
  4. Performs incremental, add-only updates
  5. Prevents duplicate record creation
  6. Applies updates in batches for efficiency
  7. Does not overwrite or delete existing records
- When to Use
  1. Production environments requiring safe updates
  2. Incremental DNS synchronization workflows
  3. Managing services with multiple IP mappings
  4. Integration with Excel-based operational processes
  5. Scenarios requiring idempotent execution

Bash vs Python 

| Feature             | Bash (CLI) | Python (SDK) |
| ------------------- | ---------- | ------------ |
| Bulk Updates        | ✅         | ✅           |
| Incremental Updates | ❌         | ✅           |
| Overwrite Risk      | High       | Low          |
| Ease of Use         | Easy       | Moderate     |
| Production Ready    | ⚠️         | ✅           |



## Conclusion 

  Automating DNS record creation in OCI Private Zones significantly improves efficiency and reduces manual effort.
  
  By combining:
  
  Bash → simple and fast bulk updates
  Python → intelligent, safe, incremental updates

