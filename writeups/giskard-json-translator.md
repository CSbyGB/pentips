# Giskard - JSON Translator

- [Link to challenge](https://red.giskard.ai/challenges/broken-formatting/json-translator)

## Instructions

![Instructions](../.res/2025-08-18-06-40-32.png)

## Resolution

The tricky thing here was trying to figure how to insert a json char to break it.  
At first I tried with `{` then with `[`
It ended up working with double quotes `"`, within the sentence to translate.  
The one that actually did the trick where the ones around the `"ça"`
![](../.res/2025-07-25-15-59-06.png)  
