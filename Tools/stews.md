# STEWS

![STEWS](../.res/2023-12-06-09-57-13.png)

Security Testing and Enumeration of WebSockets

- [Official repository](https://github.com/PalindromeLabs/STEWS)
- To install STEWS you first need to clone the directory.

```bash
git clone https://github.com/PalindromeLabs/STEWS.git
```

## Discovery

- For the discovery module you need to install the dependencies `jq` and `zgrab2`
- For `jq` it is pretty straightforward you just need to `sudo apt install jq`
- For `zgrab2` you can download the binary they provide in the documentation. Here is the documentation for [STEWS (discovery module)](https://github.com/PalindromeLabs/STEWS/blob/main/discovery/README.md), [here](https://github.com/PalindromeLabs/zgrab2/releases/download/v0.1.7/zgrab2) is the binary.
- Take the binary and cp it in your binary folder `sudo cp zgrab2 /usr/local/bin`
- Then we can modify the file known-endpoints.txt to add our endpoints.
- Finally `./STEWS-discovery.sh`

## Fingerprinting

- To install the fingerprinting module you can use `pip3 install -r requirements.txt` or `python3 -m pip install -r requirements.txt`
- `python3 STEWS-fingerprint.py -v -a -u your-url` with -a you will do all tests.

## Vulnerability Detection

- To install the vuln-detect module you can use `pip3 install -r requirements.txt` or `python3 -m pip install -r requirements.txt`
- Then use `python3 STEWS-vuln-detect.py -h` and test out what you wish to do.