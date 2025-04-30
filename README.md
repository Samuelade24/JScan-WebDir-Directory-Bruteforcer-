import requests

target = "http://testphp.vulnweb.com"
wordlist = ["admin", "secured", "CVS", "vendor"]  # Replace with your wordlist

for path in wordlist:
    url = f"{target}/{path}"
    response = requests.get(url)
    if response.status_code == 200:
        print(f"[+] Found: {url}")
    elif response.status_code == 403:
        print(f"[!] Restricted: {url}")
