Project Description

This PowerShell tool automates the process of web directory enumeration and admin panel detection, helping security professionals identify exposed administrative interfaces and sensitive directories on web servers. The tool leverages common directory brute-forcing techniques to uncover hidden paths and assesses the security of discovered admin panels.

target = "http://testphp.vulnweb.com"

Languages and Utilities Used
PowerShell (Primary scripting language)

dirb (Directory brute-forcing tool)

gobuster (Modern directory/file brute-forcing tool)

curl (For HTTP requests and credential testing)

Environments Used
Windows 10/11 (21H2 or later)

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

Program Walk-through
<p align="center"> Launch the utility: <br/> <img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Tool Launch"/> <br /> <br /> Enter target URL: <br/> <img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="URL Input"/> <br /> <br /> Select scan intensity: <br/> <img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Scan Options"/> <br /> <br /> Review discovered directories: <br/> <img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Directory Results"/> <br /> <br /> Admin panel test results: <br/> <img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Admin Panel Test"/> <br /> <br /> Sensitive data exposure report: <br/> <img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Data Exposure"/> <br /> <br /> Final vulnerability report: <br/> <img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Final Report"/> </p>


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
