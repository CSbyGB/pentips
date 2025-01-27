# Ollama

## Install

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

## Run

```bash
ollama run llama3
# Prompt away :D
# quit the prompt
/bye
```

## Use ollama in Python scripts

```bash
pip install ollama
```

You can then use it with this python code  

```python
import ollama

stream = ollama.chat(
    model='mistral' # we can choose another one such as llama2 or llama3
    messages=[{'role':'user', 'content': 'Your prompt here'}],
    stream=True,
)
# Print the output
for chunk in stream:
    print(chunk['message']['content'], end='', flush=True)
```

## Resources

- [Run llama3 locally - datacamp](https://www.datacamp.com/tutorial/run-llama-3-locally)
- [How to use Ollama in Python in 4 Minutes! | A QUICK Tutorial! - Quick Tutorials](https://youtu.be/Nox89Narr84?si=V2vJlVJXaBic59i3)