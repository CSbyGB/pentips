# Nuclei

> Notes from my practice but also from XSSrat course

Nuclei is an open source and community powered vulnerability scanner.

- [Github repo](https://github.com/projectdiscovery/nuclei)
- [Official Documentation](https://nuclei.projectdiscovery.io/nuclei/get-started/)
- [Official blog](https://blog.projectdiscovery.io/)

## Installation

Nuclei requires the latest version of GO

### The easy way

- `sudo apt update`
- `sudo apt install nuclei`

> Or you can install Go first and then nuclei as follows as per the official documentation.

### Official way

#### Install Go

- `$ rm -rf /usr/local/go && tar -C /usr/local -xzf go1.20.linux-amd64.tar.gz`
- `export PATH=$PATH:/usr/local/go/bin` (put this in `$HOME/.profile or /etc/profile`)
- More info [here](https://go.dev/doc/install)
- `go env -w GOPATH=$HOME/go`
- More info [here](https://github.com/golang/go/wiki/SettingGOPATH)

##### Install go with snapd

- `sudo systemctl start snapd`
- `sudo snap install go --classic`

#### Install Nuclei

- `go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest`

### Install with Brew

#### Install brew

- `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
- Add Homebrew to your PATH
  - `(echo; echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"') >> /home/gabrielle/.profile`
  - `eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"`
- Install gcc (recommended by brew)
  - `brew install gcc`

#### Install nuclei

- `brew install nuclei`

### Run nuclei

- `nuclei -u https://example.com/` (it can be an url or an host)

### Nuclei Templates

- [Github repo](https://github.com/projectdiscovery/nuclei-templates)
- [Official documentation](https://nuclei.projectdiscovery.io/templating-guide/)

#### Anatomy of a template

- ID
- Metadata
  - Name author description tag
- Requests
  - Method
  - Path
  - Redirects:TRUE
  - MAX-REDIRECTS:3

##### Dynamic variables

- `GE{{BASEURL}}` Replaced on runtime with the input url (target file)
- `{{ROOTURL}}` Replaced on runtime by root url (target file)
- `{{HOSTNAME}}` Replaced on runtime by hostname including port of the target
- `{{HOST}}` Replaced on runtime in the request by host (target file)
- `{{PORT}}` Replaced on runtime by input port (target file)
- `{{PATH}}` Replaced on runtime by input path (target file)