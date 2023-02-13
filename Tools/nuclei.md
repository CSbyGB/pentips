# Nuclei

Nuclei is an open source and community powered vulnerability scanner.

- [Github repo](https://github.com/projectdiscovery/nuclei)
- [Official Documentation](https://nuclei.projectdiscovery.io/nuclei/get-started/)

## Installation

Nuclei requires the latest version of GO

### The easy way

- `sudo apt update`
- `sudo apt install nuclei`

> Or you can install Go first and then nuclei as follows as per the official documentation.

### Install Go

- `$ rm -rf /usr/local/go && tar -C /usr/local -xzf go1.20.linux-amd64.tar.gz`
- `export PATH=$PATH:/usr/local/go/bin` (put this in `$HOME/.profile or /etc/profile`)
- More info [here](https://go.dev/doc/install)
- `go env -w GOPATH=$HOME/go`
- More info [here](https://github.com/golang/go/wiki/SettingGOPATH)

### Install Nuclei

- `go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest`

### Run nuclei

- `nuclei -u https://example.com/` (it can be an url or an host)

### Nuclei Templates

- [Github repo](https://github.com/projectdiscovery/nuclei-templates)
- [Official documentation](https://nuclei.projectdiscovery.io/templating-guide/)