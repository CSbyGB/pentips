# Code Review

> Notes from my practice.

## General Methodology

It is possible to do manual grep for some classes. If the codebase is small you can read everything. It is wiser to install an ide. Usually in code review, you look for the sources first and see if there are sinks that are reachable, but often there is more code than time, so I prefer to cherry pick some sink that would be critical and look if there is a corresponding source.  
You can exclude the tests from a code review after checking if sensitive data like production secrets (password etc) are not stored in it.)
When you don't have enough time it is interesting to review in 1 or 2 hours the summary of the [ASVS (owasp appsec verification standard)](https://owasp.org/www-project-application-security-verification-standard/) to make corresponding greps.  
In general it is always important to follow the inputs and see where they go. We can also review bug patterns

## Dangerous functions in java

To avoid vulnerabilities like command injection or code injection `exec()`, `eval()`  

## File upload - check

- Always check for unauthenticated file uploads

## Enumerate entry points

> thanks to my colleague Marc André for this one liner

- `grep -r -A2 -E '@(Get|Post|Put|Delete)Mapping' *`

## Tools

### Spotbugs

On a Unix, Linux, or macOS system, run the $SPOTBUGS_HOME/bin/spotbugs script, or run the command java -jar $SPOTBUGS_HOME/lib/spotbugs.jar to run the SpotBugs GUI.

- [Documentation](https://spotbugs.readthedocs.io/en/stable/)

### Other tools

- SemGrep
- FindSecBugd (analyse dynamique)
- EsPReSSO plugin burp for oAuth: will analyze and describe communitcation with oAuth

## List of weaknesses to look for

- Hardcoded credentials or secrets (grep on "password", "key", "secret")
- Information leak
- Missing security flags
- Weak password hashing mechanism
- Cross-Site Scripting
- No CSRF protection
- Directory listing
- Crypto issue
- Signature bypass
- Authentication bypass
- Authorization bypass
- Remote Code Execution
- Grep on comments
- Grep for file upload functionalities "upload"

## Useful linux commands

- `curl "https://target.com/" | grep -oP '(https*://|www\.)[^ ]*'` Extract all URL from Source Code (thanks Savan Patel on Linkedin for this command)
- send curl to proxy `export https_proxy=http://127.0.0.1:8080`
- [Curl for multipart form data](https://stackoverflow.com/questions/46960397/using-curl-for-multipart-form-data-with-a-file-upload)

## Resources

- [Bugs Patterns](https://find-sec-bugs.github.io/bugs.htm)
- [Cheat Sheet de Snyk 10 Java security best practices](https://res.cloudinary.com/snyk/raw/upload/v1568658651/Cheat_Sheet-_10_Java_Security_Best_Practices.pdf)
- [OWASP Top 10: How to Find Vulnerabilities in Your Java Applications](https://www.kiuwan.com/blog/)
- [OWASP Code Review guide](https://owasp.org/www-pdf-archive/OWASP_AlphaRelease_CodeReviewGuide2.0.pdf)
- [Best practice angular](https://snyk.io/wp-content/uploads/Angular-Security-Best-Practices-1.pdf)