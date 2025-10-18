Local LLM Setup for OpenCode using Docker + Ollama (Optimized for Mac Studio M2 Ultra)

This guide explains how to run a local Large Language Model (LLM) using Ollama, optimized for Apple Silicon (M2 Ultra) performance, and connect it to OpenCode for private, high-speed AI-assisted development.

⸻

1. Why Install Ollama via Homebrew Instead of Docker

On macOS, Ollama installed natively (via Homebrew) is significantly faster than running it in Docker Desktop.

⚡ Performance Comparison

Aspect	Brew (Host Install)	Docker Desktop (macOS)
GPU Access	✅ Uses Metal (Apple GPU acceleration)	❌ CPU-only (Docker can’t access Metal)
Memory Overhead	Low	Higher (VM + container)
Latency	Lower	Slightly higher
Best Use	Local AI workloads	Multi-service stacks, UI containers

Recommendation: Install Ollama directly on your Mac for model hosting and use Docker only for UI layers (e.g., OpenWebUI).

⸻

2. Install Ollama on macOS (Host Setup)

Installation

brew install ollama
ollama serve

Pull Recommended Models

# Fast and tools-capable (best default)
ollama pull qwen2.5-coder:latest

# Vision model (for image-based prompts)
ollama pull llama3.2-vision:latest

# Lightweight utility model
ollama pull phi3:mini

Verify Models

ollama list

Expected output:

NAME                      ID              SIZE      MODIFIED
qwen2.5-coder:latest      ...             8.1 GB    just now
llama3.2-vision:latest    ...             7.8 GB    just now
phi3:mini                 ...             2.1 GB    just now


⸻

3. Configure OpenCode (Default = Qwen2.5 Coder)

Edit your OpenCode configuration:

~/.config/opencode/opencode.json

{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": { "baseURL": "http://localhost:11434/v1" },
      "models": {
        "qwen2.5-coder:latest": { "name": "Qwen2.5 Coder (tools-capable, fast)" },
        "llama3.2-vision:latest": { "name": "Llama 3.2 Vision (image + text)" },
        "phi3:mini": { "name": "Phi-3 Mini (fast utility)" }
      }
    }
  },
  "model": "ollama/qwen2.5-coder:latest",
  "small_model": "ollama/phi3:mini"
}

Why Qwen2.5-Coder
	•	Supports tools/function-calling, which OpenCode’s agent features need.
	•	Runs fast on M2 Ultra with Metal acceleration.
	•	Optimized for coding, refactoring, and testing workflows.

⸻

4. Optional: Docker Compose for UI (OpenWebUI)

If you want a local chat UI that connects to your host’s Ollama server:

version: "3.9"
services:
  openwebui:
    image: ghcr.io/open-webui/open-webui:latest
    container_name: openwebui
    ports:
      - "3000:8080"
    environment:
      OLLAMA_BASE_URL: "http://host.docker.internal:11434"
    restart: unless-stopped

Then visit http://localhost:3000 to use OpenWebUI.

⸻

5. Test Your Setup

API Check

curl http://localhost:11434/v1/models

Test Completion

curl -s http://localhost:11434/v1/chat/completions \
  -H "Authorization: Bearer ollama" \
  -H "Content-Type: application/json" \
  -d '{
    "model":"qwen2.5-coder:latest",
    "messages":[{"role":"user","content":"Write a Python function to reverse a string."}],
    "temperature":0.3,
    "num_ctx":4096
  }'

OpenCode Check

opencode chat "Explain how dependency injection works in Python."


⸻

6. Performance Tuning (Recommended for M2 Ultra)

Setting	Value	Purpose
num_ctx	4096–8192	Context length (larger = longer reasoning)
num_predict	256–512	Speed/quality balance
threads	16–20	Uses most P-cores, leaves some system headroom
temperature	0.2–0.4	Deterministic code output

Example invocation:

ollama run qwen2.5-coder:latest --num-ctx 4096 --num-predict 384 --temperature 0.3


⸻

7. Switching Models in OpenCode

You can override your default model at any time:

# Vision tasks
opencode chat --model ollama/llama3.2-vision:latest "Describe this architecture diagram."

# Lightweight summarization
opencode chat --model ollama/phi3:mini "Summarize this README file."


⸻

✅ Summary
	•	Use Homebrew (not Docker) for the Ollama backend on macOS → full Metal GPU speed.
	•	Default model: Qwen2.5-Coder (tools + speed).
	•	Optional smaller/faster fallback: Phi-3 Mini.
	•	Optional Docker service: OpenWebUI pointing to the host Ollama.
	•	All models persist locally, offline, and private.

With this setup, your Mac Studio M2 Ultra delivers near real-time local LLM performance in OpenCode — GPU-accelerated, tool-capable, and optimized for development workflows.
