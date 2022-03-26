# WPscan

## Usage

A tool to enumerate wordpress powered website

- `wpscan --url http://url-or-ip/ --enumerate`

## Bruteforce authentication

- `wpscan --url http://url-or-ip/ --passwords /usr/share/wordlists/rockyou.txt --usernames admin`

## Troubleshot

- If you get this error `Could not find 'webrick' (>= 0) among 125 total gem(s) (Gem::MissingSpecError)` you can try to run `gem update` it should fix it
