# Stack: Ollama

This stack deploys [Ollama](https://ollama.com/), a self-hosted large language model server.

The stack assigns the following to the Ollama container:

- Port `:11434` (HTTP API)
- A memory limit of 8GB
- Access to one of the host's Nvidia GPUs
- A named volume for storing model data (this is basically just a cache of downloaded models)

## Engineering Decisions

So, why is Ollama on Sagittarius when compute should be on Hydra? 
Well, for the simple reason that Ollama can mostly run on a GPU rather than CPU - something Hydra doesn't have.
Sure, it drains some of Sagittarius' RAM, but Ollama ran so poorly on Hydra that if I don't run it on Sagittarius then it's not really worth running at all.
Fortunately, Ollama is pretty well optimised and only loads models into RAM when it's actually doing something - so most of the time it's not using much RAM at all.