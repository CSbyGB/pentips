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

## Compare 2 files

### Extract unique occurrence from 2 files

- You have to files and you want to extract unique values and put them in a new file (file1 should be the one with the more lines)
  - `awk 'FNR==NR {a[$0]++; next} !($0 in a)' file1 file2` Command found on [stackoverflow](https://stackoverflow.com/questions/4717250/extracting-unique-values-between-2-sets-files)
  - Explanation of how the code works:
    If we're working on file1, track each line of text we see.  
    If we're working on file2, and have not seen the line text, then print it.  
    Explanation of details:  

    FNR is the current file's record number  
    NR is the current overall record number from all input files  
    FNR==NR is true only when we are reading file1  
    $0 is the current line of text  
    a[$0] is a hash with the key set to the current line of text  
    a[$0]++ tracks that we've seen the current line of text  
    !($0 in a) is true only when we have not seen the line text  
    Print the line of text if the above pattern returns true, this is the default awk behavior when no explicit action is given  

## Change DNS config in kali

- [This article](http://www.infrabytes.com/change-dns-in-kali) explains it very well