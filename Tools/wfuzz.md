# WFUZZ

- Official description: "Wfuzz provides a framework to automate web applications security assessments and could help you to secure your web applications by finding and exploiting web application vulnerabilities."
- It is preinstalled on kali. Check it out on github [here](https://github.com/xmendez/wfuzz)
- [Here](https://wfuzz.readthedocs.io/en/latest/) is the documentation

## Subdomain enumeration

- `wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u 'http://lazyadmin.thm/' -H "HOST: FUZZ.lazyadmin.thm" --hw 968`
  - `hw` will filter the response that contains the number of words specified. Very convenient for notfound pages that give 200 code.
  - `hc 302` will remove all 302 responses from the output

## API Fuzz

- Check out this article [here](/web-pentest/API.md#wfuzz) to see how to use it on API
