# Wildcard Abuse

A wildcard character can be used as a replacement for other characters and are interpreted by the shell before other actions. Examples of wild cards include:

|Character|	Significance|
|---------|-------------|
|`*` | An asterisk that can match any number of characters in a file name.|
|`?`	|Matches a single character.|
|`[ ]`|	Brackets enclose characters and can match any single one at the defined position.|
|`~`	|A tilde at the beginning expands to the name of the user home directory or can have another username appended to refer to that user's home directory.|
|`-`	|A hyphen within brackets will denote a range of characters.|

- See [here](cron.md#cron-wildcards) to have an example of wildcard abuse
