# OpenCode NVIDIA Nemotron 3 Super Integration Guide – Step‑by‑Step Setup (March 2026)

**Learn how to add the brand‑new NVIDIA Nemotron 3 Super model (`nvidia/nemotron-3-super-120b-a12b`) to OpenCode** – the open‑source AI coding agent – in under 5 minutes. This guide walks you through getting your NVIDIA API key, configuring the provider, setting Nemotron as your default model, and verifying the 1M token context window. Perfect for developers who want to stay on the cutting edge of AI‑assisted coding.

---

## Table of Contents
- [Why This Guide?](#why-this-guide)
- [Prerequisites](#prerequisites)
- [Step 1: Get Your NVIDIA API Key](#step-1-get-your-nvidia-api-key)
- [Step 2: Add the Credential to OpenCode](#step-2-add-the-credential-to-opencode)
- [Step 3: Configure Nemotron 3 Super in `opencode.json`](#step-3-configure-nemotron-3-super-in-opencodejson)
- [Step 4: Restart and Verify](#step-4-restart-and-verify)
- [Advanced Configuration Options](#advanced-configuration-options)
- [Troubleshooting FAQ](#troubleshooting-faq)
- [Resources & Further Reading](#resources--further-reading)
- [About This Repository](#about-this-repository)
- [Contributing](#contributing)

---

## Why This Guide? {#why-this-guide}

The **NVIDIA Nemotron 3 Super** model was released on **March 10, 2026** and is not yet cached in OpenCode’s automatic model list. As a result, the model ID `nvidia/nemotron-3-super-120b-a12b` does not appear in the `/models` dropdown until you manually add it. This guide eliminates the guesswork, giving you exact, copy‑and‑paste configuration that works today.

**Target keywords**: OpenCode NVIDIA Nemotron 3 Super, how to add custom model to OpenCode, nvidia/nemotron-3-super-120b-a12b setup, OpenCode provider configuration, AI coding agent tutorial.

---

## Prerequisites {#prerequisites}

- [OpenCode installed](https://opencode.ai/docs/cli/) (version ≥ 1.2.0)
- A free or paid account at [NVIDIA Build](https://build.nvidia.com/) to generate an API key
- Basic familiarity with JSON and the terminal

---

## Step 1: Get Your NVIDIA API Key {#step-1-get-your-nvidia-api-key}

1. Go to [https://build.nvidia.com/](https://build.nvidia.com/)
2. Sign up or log in
3. Navigate to **API Keys** → **Create New Key**
4. Copy the generated key (it starts with `nvapi-`)

> **Tip**: New accounts receive 1,000 free inference credits – enough to test the model.

---

## Step 2: Add the Credential to OpenCode {#step-2-add-the-credential-to-opencode}

Run the following command in your terminal:

```bash
opencode auth add
```

When prompted:
1. Choose **Nvidia** (or type `nvidia` if it isn’t listed)
2. Paste your API key when asked
3. Press **Enter**

You can verify the credential was stored with:

```bash
opencode auth list
```

You should see an entry similar to:
```
●  Nvidia  90mapi
```

---

## Step 3: Configure Nemotron 3 Super in `opencode.json` {#step-3-configure-nemotron-3-super-in-opencodejson}

Open your global OpenCode config file:

- **macOS/Linux**: `~/.config/opencode/opencode.json`
- **Windows**: `%USERPROVIDER%\.config\opencode\opencode.json`

If the file does not exist, create it. Replace (or add) the following JSON block:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nvidia/nvidia/nemotron-3-super-120b-a12b",
  "provider": {
    "nvidia": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "NVIDIA NIM",
      "options": {
        "baseURL": "https://integrate.api.nvidia.com/v1"
      },
      "models": {
        "nvidia/nemotron-3-super-120b-a12b": {
          "name": "Nemotron 3 Super",
          "limit": {
            "context": 1000000
          }
        }
      }
    }
  }
}
```

### What each part does
| Key | Purpose |
|-----|---------|
| `model` | Sets Nemotron 3 Super as the default model for new sessions |
| `provider.nvidia.npm` | Uses the OpenAI‑compatible adapter (required for NVIDIA NIM) |
| `provider.nvidia.options.baseURL` | The NVIDIA NIM API endpoint |
| `provider.nvidia.models` | Registers the exact model ID with a readable name and context limit |
| `limit.context` | Declares the 1,000,000‑token context window (helps OpenCode manage token budget) |

> **Note**: If you used a different provider ID when running `opencode auth add` (for example `nim`), replace the top‑level `nvidia` key with that ID.

---

## Step 4: Restart and Verify {#step-4-restart-and-verify}

1. **Restart OpenCode** completely (close the TUI/Web/UI and relaunch).
2. Open the model selector with `/models`.
3. You should see **`nvidia/nemotron-3-super-120b-a12b`** listed (often shown as “Nemotron 3 Super”).
4. Select it, or simply start a new session – it will be used automatically because it’s set as the default model.
5. Test the long context: paste a large text block ( >100k tokens ) and ask a question that requires the model to see the whole input; the model should respond correctly, confirming the 1M window works.

---

## Advanced Configuration Options {#advanced-configuration-options}

### Setting a Cheaper Small Model
For lightweight tasks like session titles, you may want a faster/cheaper model:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nvidia/nvidia/nemotron-3-super-120b-a12b",
  "small_model": "anthropic/claude-haiku-4-5",
  "provider": {
    "nvidia": { /* … same as above … */ },
    "anthropic": {
      "name": "Anthropic",
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    }
  }
}
```

### Custom Variants (Reasoning vs. Fast)
Create different configurations for the same model and switch with `/variant_cycle`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nvidia": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "NVIDIA NIM",
      "options": {
        "baseURL": "https://integrate.api.nvidia.com/v1"
      },
      "models": {
        "nvidia/nemotron-3-super-120b-a12b": {
          "name": "Nemotron 3 Super",
          "limit": {
            "context": 1000000
          },
          "variants": {
            "reasoning": {
              "reasoningEffort": "high"
            },
            "fast": {
              "reasoningEffort": "low",
              "textVerbosity": "low"
            }
          }
        }
      }
    }
  }
}
```

### Using Via Command Line (One‑off)
Override the model for a single session without changing config:

```bash
opencode run "Explain the Transformer architecture in detail" -m nvidia/nvidia/nemotron-3-super-120b-a12b
```

---

## Troubleshooting FAQ {#troubleshooting-faq}

**Q: The model does not appear in `/models`.**  
A: 1️⃣ Validate your JSON: `python3 -m json.tool ~/.config/opencode/opencode.json`  
   2️⃣ Ensure the provider ID matches what you used in `opencode auth add` (`opencode auth list` shows it).  
   3️⃣ Restart OpenCode after saving the file.

**Q: I get an authentication error.**  
A: Verify the key is correctly stored: `opencode auth list`. If unsure, delete and re‑add: `opencode auth rm nvidia` then `opencode auth add`.

**Q: The model responds but seems to forget earlier parts of a long prompt.**  
A: Confirm `limit.context` is set to `1000000` (not `1048576` etc.). Some frontends impose their own limits – test in the raw OpenCode TUI.

**Q: I want to use a different NVIDIA model (e.g., a Llama variant).**  
A: Replace the model ID in both the `model` field and inside `provider.nvidia.models` with the desired ID from [NVIDIA Build](https://build.nvidia.com/explore/discover).

---

## Resources & Further Reading {#resources--further-reading}

- [NVIDIA Build – Get API Key](https://build.nvidia.com/)
- [NVIDIA Nemotron 3 Super Model Card](https://build.nvidia.com/nvidia/nemotron-3-super-120b-a12b)
- [OpenCode Documentation](https://opencode.ai/docs/)
- [AI SDK Providers Guide](https://ai-sdk.dev/providers/)
- [OpenCode GitHub Repository](https://github.com/anomalyco/opencode)
- [Models.dev – NVIDIA Nemotron 3 Super](https://models.dev/provider/nvidia/nemotron-3-super-120b-a12b)

---

## About This Repository {#about-this-repository}

This repository is maintained by **open‑source enthusiasts** who believe in making cutting‑edge AI accessible to everyone. As the primary maintainer, I am dedicated to:
- Keeping this guide updated with the latest model releases
- Providing clear, accurate instructions for developers of all levels
- Building a community around open‑source AI tooling
- Ensuring every developer can benefit from models like Nemotron 3 Super

**Star this repo** if you found it helpful, and **share it** with fellow developers who want to stay on the cutting edge of AI‑assisted development!

*Last updated: March 12, 2026*  
*For the brand‑new NVIDIA Nemotron 3 Super release*

---

## Contributing {#contributing}

We welcome contributions! Whether it’s fixing a typo, adding a new configuration example, or translating the guide into another language, please:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/your-idea`)
3. Make your changes
4. Open a Pull Request

Please read the [Contributing Guidelines](CONTRIBUTING.md) for details.

---

<small>© 2026 tencent-source. Licensed under the MIT License.</small>