# PATH abuse

## Enumeration

- `echo $PATH` or `env | grep PATH` check the content of the env var PATH. Creating a script or program in a directory specified in the PATH will make it executable from any directory on the system.

## Exploitation

- Adding `.` to a user's PATH adds their current working directory to the list. For example, if we can modify a user's path, we could replace a common binary such as ls with a malicious script such as a reverse shell. If we add `.` to the path by issuing the command `PATH=.:$PATH` and then `export PATH`, we will be able to run binaries located in our current working directory by just typing the name of the file (i.e. just typing ls will call the malicious script named ls in the current working directory instead of the binary located at /bin/ls).
