# Stack: Ollama

This stack deploys [Ollama](https://ollama.com/), a self-hosted large language model server.

The stack assigns the following to the Ollama container:

- Port `:11434` (HTTP API)
- A memory limit of 8GB
- Access to one of the host's Nvidia GPUs
- A named volume for storing model data (this is basically just a cache of downloaded models)