![Distributed AI Architecture Diagram](/images/ai-architecture.jpg)

---
title: "Operation: Distributed Private AI"
date: 2026-02-25
draft: false
---

## // The Local LLM Architecture

With the rapid expansion of AI, I wanted the capabilities of a Large Language Model (LLM) without sending my data, network logs, or automation scripts out to a public cloud server. 

To solve this, I engineered a distributed, completely private AI architecture utilizing my existing home lab and PC hardware.

### The Hardware Split
Running an LLM natively on a Raspberry Pi is incredibly slow due to hardware limitations. Instead, I split the compute from the interface:

* **The Backend Engine (Heavy Compute):** My main PC (Intel i7-13700K, RTX 4070 Ti) runs **Ollama**. This handles all the complex neural network math and model hosting (currently utilizing Meta's Llama 3), keeping inference times lightning fast.
* **The Edge Node (The Interface):** My Raspberry Pi, running DietPi, hosts a Docker container running **Open WebUI**. This acts as the web server and graphical interface.

### The Network Bridge
By opening port `11434` on the PC's firewall and binding the Ollama host to `0.0.0.0`, the Pi can successfully route prompts from the WebUI across the local network to the PC for processing.

The result is a self-hosted, ChatGPT-style interface accessible from any device on my Wi-Fi. It is completely offline, highly secure, and utilizes my GPU's VRAM for enterprise-grade response speeds.