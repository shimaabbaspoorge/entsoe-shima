# GRIDos Connect - ENTSO-E Congestion Data Integration

Integration flow for fetching and storing congestion data from the ENTSO-E Transparency Platform API for GE Vernova's GRIDos Connect platform.

## Overview

This flow retrieves congestion management data (Document Type A25) from ENTSO-E's API and appends it to a local data file for historical tracking and analysis.

### Data Retrieved
- **Document Type**: A25 (Allocation result document)
- **Business Type**: B10 (Congestion management)
- **Contract Type**: A01 (Daily)
- **Domain**: 10Y1001A1001A82H
- **Resolution**: 15-minute intervals (PT15M)
- **Data Points**: Position and price amount per interval
  //this api is for 
## Prerequisites

- GE Vernova MeterCloud Integration Platform
- ENTSO-E API Security Token ([Register here](https://transparency.entsoe.eu/))
- Kotlin/JVM environment
- Access to GRIDos deployment resources

## Project Structure
gridos-connect-entsoe/ 
├── src/ 
│ └── main/ 
│ ├── kotlin/
│ │ └── flows/
│ │ └── EntsoeFlow.kt # Main flow definition
│ └── resources/
│ └── deployment/
│ └── gridos/
│ └── presales/
│ └── secrets/
│ └── token.txt # ENTSO-E API token (not in Git)
├── data/
│ └── entsoe_data.xml # Output file (auto-created)
├── .gitignore
└── README.md


## Setup Instructions

### 1. Configure ENTSO-E Token

**Option A: File-based (Development)**
```bash
# Create the secrets directory
mkdir -p src/main/resources/deployment/gridos/presales/secrets

# Add your token 
echo "YOUR_ENTSOE_TOKEN_HERE" > src/main/resources/deployment/gridos/presales/secrets/token.txt

# Configure Date Range
Edit EntsoeFlow.kt:

var startDate: String = "202510312200"  // Format: YYYYMMDDHHmm (UTC)
var endDate: String = "202511032200"    // Format: YYYYMMDDHHmm (UTC)
Date Format: YYYYMMDDHHmm in UTC timezone

# Configure Domain (Optional)
To fetch data for different regions, modify:

"out_Domain=10Y1001A1001A82H",  // Change to your target domain
"in_Domain=10Y1001A1001A82H",   // Change to your target domain

--->  Data is appended to data/entsoe_data.xml with the following structure:

<!-- Data from 2025-11-01 -->
<Publication_MarketDocument xmlns="urn:iec62325.351:tc57wg16:451-3:publicationdocument:7:0">
    <mRID>1606d8a0533e4545bda8c88517be0056</mRID>
    <type>A25</type>
    <createdDateTime>2025-11-03T11:47:10Z</createdDateTime>
    <period.timeInterval>
        <start>2025-10-31T23:00Z</start>
        <end>2025-11-01T23:00Z</end>
    </period.timeInterval>
    <TimeSeries>
        <businessType>B10</businessType>
        <currency_Unit.name>EUR</currency_Unit.name>
        <price_Measure_Unit.name>MWH</price_Measure_Unit.name>
        <Period>
            <resolution>PT15M</resolution>
            <Point>
                <position>1</position>
                <price.amount>6057.19</price.amount>
            </Point>
            <!-- More points... -->
        </Period>
    </TimeSeries>
</Publication_MarketDocument>

<!-- Data from 2025-11-02 -->
<Publication_MarketDocument>
    <!-- Next day's data... -->
</Publication_MarketDocument>


