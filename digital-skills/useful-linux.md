# Useful tools and command in linux for general tasks

## Convert HEIC to JPG

- `sudo apt install libheif-examples` install heif-convert
- For one file `heif-convert -q 85 input.HEIC output.JPG`
- For multiple files `for file in *.HEIC; do heif-convert $file ${file/%.HEIC/.JPG}; done`
