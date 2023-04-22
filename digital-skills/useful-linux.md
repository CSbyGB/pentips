# Useful tools and command in linux for general tasks

## Change your hostname

- `sudo nano /etc/hostname`
- `sudo nano /etc/hosts`
- `sudo reboot`
- `hostname` should give you your newly chosen hostname

## Debug command not found

> Works only in bash

This annoying message when you need something that is not installed.

- `sudo apt install command-not-found` will install an utility that will provide information when you will get this error.
- Alternatively you can use `apt-cache search command-name` it will tell what to install

> *See [this thread](https://unix.stackexchange.com/questions/355023/showmount-command-not-found-how-do-i-know-what-to-install) for more info*

## Debug apt

- If you get this error `The following signatures were invalid`
- `sudo wget -q -O - https://archive.kali.org/archive-key.asc  | sudo apt-key add`
- More info on [this thread](https://unix.stackexchange.com/questions/421985/invalid-signature-when-trying-to-apt-get-update-on-kali)

## Debug snap

- If you get this error `snap-confine has elevated permissions and is not confined but should be. Refusing to continue to avoid permission escalation attacks`
- `sudo apparmor_parser -r /etc/apparmor.d/*snap-confine*`
- `sudo apparmor_parser -r /var/lib/snapd/apparmor/profiles/snap-confine*`
- More info [here](https://stackoverflow.com/questions/70053614/snap-confine-has-elevated-permissions-and-is-not-confined-but-should-be-refusin)

## File conversions

### HEIC to JPG

- `sudo apt install libheif-examples` install heif-convert
- For one file `heif-convert -q 85 input.HEIC output.JPG`
- For multiple files `for file in *.HEIC; do heif-convert $file ${file/%.HEIC/.JPG}; done`

### Images to PDF

- You can do this with one image or multiple to put them all in one pdf
- `convert *.png document.pdf` convert all png files of current folder in one pdf
- `convert document.jpg document.pdf` convert the file `document.jpg` in to a pdf named `document.pdf`

> Note: if you get this error, this [article](https://suay.site/?p=2369) is great. You just need to add this line `<policy domain="coder" rights="read | write" pattern="PDF" />` before `</policymap>` in the file `/etc/ImageMagick-6/policy.xml` (the path can be different depending on your version of ImageMagick)

### webp to jpg

- `convert *.webp *.jpg`

### PDF to images

- You can do the other way around `pdfimages file.pdf extracted-images/` you can add the lag `-png` to specify that you want the pictures to be in png format

> Note this will get every image from the PDF file. If you want every slide as images this next command is the one you need

- `pdftoppm -png file.pdf file/name-you-want-to-give-to-the-images`

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

## Grep

- If you are looking for a specific thing in a file you can grep a string on it.
- `cat file.txt | grep "keyword"` looking for keyword in file.txt
- `-B 2` this option will show us the 2 lines before our keyword
- `-A 2` This option will show su the 2 lines after our keyword

## Compare 2 files

## Packets install and mamangement

### List installed packets

- `apt list --installed` (add a grep on it for example `| grep image` if you are looking for the package imagemagick)

### Install a deb package with apt

- `sudo apt install ./packet.deb`

## Unzip

- If you accidentally unzipped a file and want to delete all the files you just unzipped `unzip -Z -1 <filename.zip> | xargs -I{} rm -v {}`

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

- [This article](http://www.infrabytes.com/change-dns-in-kali) explains it very well.

## Speed up update process in Kali

- If like me you are annoyed at the time it takes to update your kali, it is possible to use a mirror.
- [This article](https://trendoceans.com/how-to-fix-kali-linux-slow-apt-update-process/) explains it very well.

> **Note: it is not the official repositories so use with caution ;)**

## Add a set title function in gnome-terminal

- Add this code in your bashrc

```bash
function set-title() {
  if [[ -z "$ORIG" ]]; then
    ORIG=$PS1
  fi
  TITLE="\[\e]2;$*\a\]"
  PS1=${ORIG}${TITLE}
}
```

- `source .bashrc`
- Then to set a title you will just need to do `set-title mytitle`

> [Source](https://code.mendhak.com/set-terminal-title/)