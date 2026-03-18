# Run LLMs Locally on Mac with LM Studio

Running LLMs on your own machine lets you experiment, build prototypes, and handle data privately without relying on a cloud API. [LM Studio](https://lmstudio.com/) has become a popular tool for this, especially with its support for MLX to accelerate AI compute on Apple Silicon.

![LM Studio Homepage](https://getdeploying.com/static/img/content/lm-studio-homepage.7dde9dc3b440.png)

LM Studio Homepage

This guide will walk you through setting up LM Studio, enabling MLX, and chatting with a local model.

Table of Contents

- What You'll Need
- What is MLX?
- Looking Forward: M5's Neural Accelerators
- Step 1: Get LM Studio
- Step 2: Find & Download a Model
- Step 3: Enable MLX and Chat
- When to Use Local vs. Cloud
- Next Steps

## What You'll Need

- **CPU/GPU:** A Mac with an Apple Silicon chip (M1, M2, M3, M5, etc). MLX acceleration is designed specifically for this hardware.
    
- **Memory (RAM):** Due to Apple's unified memory architecture, your available RAM will determine the size of the models you can run:
    
    - 8GB RAM: You'll be limited to smaller models (eg. 3B) or heavily quantized 7B models.
    - 16GB RAM: The sweet spot. You can comfortably run most 7B and 8B models at good quality.
    - 32GB+ RAM: You can run larger, more powerful models (eg. 13B to 34B).

## What is MLX?

MLX is an open-source machine learning framework from Apple, designed specifically for Apple Silicon. Unlike traditional frameworks that treat the CPU and GPU as separate, MLX leverages unified memory. This means it can access data from both the CPU and GPU without copying the data around, which is a major bottleneck.

For running LLMs, this means:

1. **GPU Acceleration:** The heavy lifting is done by your Mac's M-series GPU, not the CPU.
2. **Efficiency:** It's fast and memory-efficient, making it possible to run surprisingly large models on a laptop thanks to all the available memory and relatively high memory bandwidth.

## Looking Forward: M5's Neural Accelerators

Apple is continuing to invest heavily in on-device AI. The latest M5 chips, for example, now include dedicated Neural Accelerators (similar to Nvidia's tensor cores) directly within each GPU core.

These are built for one job: accelerating the matrix math (matmul) at the core of all LLMs. This is expected to dramatically speed up AI tasks, [especially the "pre-fill" stage](https://creativestrategies.com/research/m5-apple-silicon-its-all-about-the-cache-and-tensors/) (the time it takes to process your initial prompt).

While full support for these new M5 cores is still coming in a future MLX update, it's clear that the performance for local LLMs on Mac is going to get even better.

## Step 1: Get LM Studio

First, download and install the LM Studio application.

- Go to the [LM Studio website](https://lmstudio.ai/).
- Download the build for Apple Silicon (Mac).
- Drag the application to your `/Applications` folder and open it.

## Step 2: Find & Download a Model

LM Studio's home screen is a search interface for Hugging Face. You'll be downloading models in the GGUF format, which is a file format optimized for running models locally.

1. In the search bar, type the name of a model you want to try. `Qwen3 4B Thinking` is an excellent starting point, or you can search for a larger model like `Gemma 3 27B`.
    
2. In the search results, you will see various "quantized" versions of the model. Quantization is a process that shrinks the model's file size, reducing RAM usage at a small cost to accuracy.
    
3. Look for a version that fits your hardware. A good general-purpose choice is a **4-bit quantization**, often labeled as `Q4_K_M`.
    
4. Click Download next to the file you've chosen. You can monitor the download progress in the "Downloads" tab at the bottom.
    

### What's a good first model?

The [qwen/qwen3-4b-thinking-2507](https://lmstudio.ai/models/qwen/qwen3-4b-thinking-2507) is highly recommended. It's small, fast, and very capable for most chat and coding tasks. If you have 32GB+ of RAM and want more power, try a model like [google/gemma-3-27b](https://lmstudio.ai/models/google/gemma-3-27b).

## Step 3: Enable MLX and Chat

Once your model is downloaded, you can chat with it. This is where you enable MLX.

1. Click the Chat icon (speech bubble) on the left-hand menu.
2. At the top, click Select a model to load and choose the model you just downloaded.
3. On the right-hand panel, find the Hardware Acceleration drop-down.
4. Change the setting from the default to MLX (Apple Silicon GPU).
5. Wait a moment for the model to load into memory (you'll see a progress bar at the top).

Once loaded, you can start a conversation in the chat box. The first response may take a moment, but subsequent responses should be significantly faster as the MLX engine is active.

## When to Use Local vs. Cloud

While a local setup is a great way to run LLMs, it's not a full replacement for a power-hungry data center.

|Feature|Local (LM Studio + MLX)|Cloud Provider (eg. Anthropic, AWS)|
|---|---|---|
|**Cost**|**Free.** Uses your own hardware|**Pay-per-token or GPU hours**, scales with usage|
|**Privacy**|**Fully private.** Data never leaves your Mac|Data is sent to a third-party vendor|
|**Performance**|**Depends** on your GPU, LLMs are power hungry|**Fast**, runs on dedicated hardware|
|**Model Access**|**Limited** by your RAM (eg. 7B-34B models)|Access to state-of-the-art, massive models (eg. Gemini 2.5 Pro)|
|**Use Case**|Prototyping, dev/test, offline use, privacy|Production apps, large-scale tasks, SOTA performance|

My recommended workflow is:

- **Develop Locally:** Use your LM Studio chat to test prompts, refine ideas, and experiment with different open-source models for free.
- **Deploy to Cloud:** When your application logic is ready for production, build it against a cloud provider's API or deploy to a cloud GPU for scale and access to the most powerful models.

Check out my list of [cloud GPU prices](https://getdeploying.com/gpus) and [LLM prices](https://getdeploying.com/llm-price-comparison) to compare your options.

## Next Steps

Now that you have a powerful LLM running on your Mac, here are a few things to try:

- **Experiment with Models:** Download a few different models (eg. a small, fast one and a larger, smarter one) to see how they compare in speed and quality.
- **Try System Prompts:** Use the "System Prompt" field in the chat UI to guide the model's behavior (eg. "You are a customer support agent").
- **Compare Hardware:** If you have access to other Macs, test the same model on an M1 vs. an M5 to see the performance gains.
- **Check Model Settings:** On the right-hand panel in the chat, try adjusting settings like "Context Length" (how much of the conversation the model remembers) to see how it affects performance.