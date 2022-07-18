# Useful tools and command in linux for general tasks

## Convert HEIC to JPG

- `sudo apt install libheif-examples` install heif-convert
- For one file `heif-convert -q 85 input.HEIC output.JPG`
- For multiple files `for file in *.HEIC; do heif-convert $file ${file/%.HEIC/.JPG}; done`

## Find things

- `find / -name "keyword" 2>/dev/null` look for something named keyword in the whole system and do not show error messages  
- `find / -type f -name ".*" 2>/dev/null` find hidden files in the system and do not show error messages

### Other options with find

- `-iname` if you the letter capitalization does not matter
- `.` to search in the current folder
- `-type d` if you are looking for a directory
- `-type f` if you are looking for a file
- `-perm 777` if you look for files with 777 perm
- `! -perm 777` if you look for files without 777 perm
- `-perm /a=x` to look for executables files
- `-exec` to execute a command on the file found example `find / -type d -perm 777 -print -exec chmod 755 {} \;`
- `-empty` to find empty files
- `-user username` use with find to get files with the user named "username"
- `-group groupname` same as user but for group
- `-mtime 5` modified in the last 5 days
- `-atime 5` accessed in the last 5 days
- `-mtime +5 –mtime -10` modify in between 5 to 10 days ago
- `-cmin -60` changed in the last 60 minutes
- `-mmin -60` modified in the last 60 minutes
- `-amin -60` accessed in the last 60 minutes
- `-size 5M` get files that are 5MB
