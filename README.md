**Project Description**

WebVuln Scanner is a PowerShell-based security assessment tool that automates web application penetration testing. It performs comprehensive vulnerability scanning including directory enumeration, admin panel detection, and credential testing with professional reporting capabilities.

target = "http://testphp.vulnweb.com"

Key Features:

Automated directory brute-forcing (using dirb/gobuster)
Admin interface detection and credential testing
Sensitive data exposure identification
Comprehensive risk assessment matrix
Professional HTML/PDF report generation
Customizable scan profiles

Installation:
# Install prerequisites
Install-Module -Name PoshRSJob -Force
Install-Module -Name ImportExcel -Force

# Clone repository
git clone https://github.com/yourusername/webvuln-scanner.git
cd webvuln-scanner

# Run tool
.\WebVulnScanner.ps1

Languages and Utilities Used: 
PowerShell (Primary scripting language)
dirb (Directory brute-forcing tool)
gobuster (Modern directory/file brute-forcing tool)
curl (For HTTP requests and credential testing)

Environments Used
Windows 10
Kali Linux (For cross-platform compatibility)
wordlist = ["admin", "secured", "CVS", "vendor"]  # Replace with your wordlist

for path in wordlist:
    url = f"{target}/{path}"
    response = requests.get(url)
    if response.status_code == 200:
        print(f"[+] Found: {url}")
    elif response.status_code == 403:
        print(f"[!] Restricted: {url}")

Features
Automated directory enumeration using multiple tools
Admin panel detection and credential testing
Sensitive data exposure identification
Risk assessment with CVE correlation
Comprehensive reporting

Usage Examples
Basic scan:
.\WebVulnScanner.ps1 -Target http://testphp.vulnweb.com

Comprehensive scan with reporting:
.\WebVulnScanner.ps1 -Target http://testphp.vulnweb.com -ScanType Full -ReportFormat HTML

Credential testing:
.\WebVulnScanner.ps1 -Target http://testphp.vulnweb.com/admin/ -CredentialFile creds.txt


**Scan Methodology:**

Reachability Check - Verify target availability
Directory Enumeration - Discover hidden paths
Admin Panel Detection - Identify management interfaces
Credential Testing - Attempt common/default logins
Vulnerability Assessment - Evaluate discovered issues
Report Generation - Create professional documentation

## Target: http://testphp.vulnweb.com
### Critical Findings:
- [x] Admin panel accessible at /admin/ with default credentials (test:test)
- [x] Sensitive data exposure (credit cards, PII)
- [x] Unprotected source control (/CVS/)

### Risk Assessment:
| Vulnerability | Severity |
|---------------|----------|
| Default credentials | Critical |
| Data exposure | Critical |
| Directory listing | High |

### Recommendations:
1. Change all default credentials immediately
2. Implement IP whitelisting for admin interfaces
3. Remove /CVS/ directory
4. Deploy WAF protection

Technical Implementation

# Core scanning function
function Invoke-WebScan {
    param(
        [string]$Target,
        [string]$ScanType = 'Standard',
        [string]$ReportFormat = 'HTML'
    )

    # Initialize results object
    $Results = @{
        Target = $Target
        StartTime = Get-Date
        Findings = @()
    }

    # Perform reachability check
    $Reachability = Test-TargetReachability -Target $Target
    $Results.Reachability = $Reachability

    # Directory enumeration
    if($ScanType -ne 'Quick') {
        $Directories = Find-Directories -Target $Target -ScanType $ScanType
        $Results.Directories = $Directories
    }

    # Generate report
    New-Report -Results $Results -Format $ReportFormat
}


# WebDirectoryScanner.ps1

param(
    [string]$Url,
    [ValidateSet('Low','Medium','High')]
    [string]$Intensity = 'Medium',
    [switch]$TestCredentials
)

# Import modules
. .\modules\directory-enum.ps1
. .\modules\admin-panel-test.ps1
. .\modules\report-generator.ps1

function Main {
    Write-Host "=== Web Directory Enumeration Scanner ===" -ForegroundColor Cyan
    
    # Validate URL
    if (-not $Url) {
        $Url = Read-Host "Enter target URL (e.g., http://example.com)"
    }
    
    # Perform directory enumeration
    $discoveredPaths = Invoke-DirectoryEnumeration -Url $Url -Intensity $Intensity
    
    # Test for admin panels
    $adminResults = Test-AdminPanels -Paths $discoveredPaths -Url $Url
    
    # If enabled, test default credentials
    if ($TestCredentials) {
        $credentialResults = Test-DefaultCredentials -AdminPanels $adminResults
    }
    
    # Generate report
    New-ScanReport -Url $Url -Paths $discoveredPaths -AdminResults $adminResults -CredentialResults $credentialResults
}

Main

# WebDirectoryScanner.ps1

param(
    [string]$Url,
    [ValidateSet('Low','Medium','High')]
    [string]$Intensity = 'Medium',
    [switch]$TestCredentials
)

# Import modules
. .\modules\directory-enum.ps1
. .\modules\admin-panel-test.ps1
. .\modules\report-generator.ps1

function Main {
    Write-Host "=== Web Directory Enumeration Scanner ===" -ForegroundColor Cyan
    
    # Validate URL
    if (-not $Url) {
        $Url = Read-Host "Enter target URL (e.g., http://example.com)"
    }
    
    # Perform directory enumeration
    $discoveredPaths = Invoke-DirectoryEnumeration -Url $Url -Intensity $Intensity
    
    # Test for admin panels
    $adminResults = Test-AdminPanels -Paths $discoveredPaths -Url $Url
    
    # If enabled, test default credentials
    if ($TestCredentials) {
        $credentialResults = Test-DefaultCredentials -AdminPanels $adminResults
    }
    
    # Generate report
    New-ScanReport -Url $Url -Paths $discoveredPaths -AdminResults $adminResults -CredentialResults $credentialResults
}

Main
