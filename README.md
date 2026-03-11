# OpenCode + NVIDIA Nemotron 3 Super Integration Guide

**OpenCode** is the open-source AI coding agent that lets you connect to any LLM provider. This guide shows how to integrate the brand-new **NVIDIA Nemotron 3 Super** model (released March 12, 2026) into OpenCode so you can start using its 1M context window and superior agentic capabilities right away.

## 🚀 Quick Start

If you already have OpenCode installed, add the Nemotron model in **under 60 seconds**:

1. **Get your NVIDIA API key**  
   Sign up at [NVIDIA Build](https://build.nvidia.com/) → Generate API key → Free 1,000 inference credits

2. **Add the credential to OpenCode**  
   ```bash
   opencode auth add
   # Select "Nvidia" (or type "nvidia")
   # Paste your API key when prompted
   ```

3. **Configure the model in OpenCode**  
   Add this to your global config (`~/.config/opencode/opencode.json`):
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

4. **Restart OpenCode** and run `/models` → you should see **Nemotron 3 Super** as an option.

That's it! You're now running the latest open, efficient hybrid Mamba-Transformer MoE from NVIDIA.

---

## 🔧 Detailed Explanation

### Why This Works

OpenCode uses the [AI SDK](https://ai-sdk.dev/) under the hood, which speaks OpenAI-compatible APIs out of the box. NVIDIA NIM (NVIDIA Inference Microservices) provides exactly that—a drop-in OpenAI-compatible endpoint at `https://integrate.api.nvidia.com/v1`.

By adding a small provider block in `opencode.json`, we teach OpenCode:
- Where to send requests (`baseURL`)
- What models are available (`models`)
- How to display them (`name`)
- Special limits like the 1M token context window (`limit.context`)

### Understanding the Config

Here's what each part does:

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

- **`model`**: Sets Nemotron as your default model for new sessions
- **`provider.nvidia.npm`**: Tells OpenCode to use the OpenAI-compatible adapter
- **`provider.nvidia.options.baseURL`**: The NVIDIA NIM API endpoint
- **`provider.nvidia.models`**: Registers the specific model ID with a friendly name and context limit

### Verifying Your Setup

After saving the config and restarting OpenCode:

1. Run `/models` in the TUI
2. You should see `nvidia/nemotron-3-super-120b-a12b` listed
3. Select it or just start chatting—it's your default now
4. Test with a long context prompt to verify the 1M window works

---

## 📊 Model Specifications

**Nemotron 3 Super** (`nvidia/nemotron-3-super-120b-a12b`):
- **Architecture**: Hybrid Mamba-Transformer MoE
- **Active Parameters**: 12B
- **Total Parameters**: 120B
- **Context Length**: 1,000,000 tokens
- **Key Strengths**: Agentic reasoning, coding, planning, tool calling
- **Efficiency**: 2.2x higher throughput than GPT-OSS-120B
- **License**: [NVIDIA Nemotron Open Model License](https://www.nvidia.com/en-us/agreements/enterprise-software/nvidia-nemotron-open-model-license/)

---

## 🛠️ Advanced Configuration

### Setting a Cheaper Small Model

For lightweight tasks like session titles, you might want a faster/cheaper model:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nvidia/nvidia/nemotron-3-super-120b-a12b",
  "small_model": "anthropic/claude-haiku-4-5",
  "provider": {
    "nvidia": { /* ... same as above ... */ },
    "anthropic": {
      "name": "Anthropic",
      // Your Anthropic config here if you have it
    }
  }
}
```

### Custom Variants

Create different configurations for the same model:

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

Then use `/variant_cycle` to switch between them.

### Using Via Command Line

Override the model for a single session:

```bash
opencode run "Explain quantum computing" -m nvidia/nvidia/nemotron-3-super-120b-a12b
```

---

## 📚 Resources

- [NVIDIA Build - Get API Key](https://build.nvidia.com/)
- [NVIDIA Nemotron 3 Super Model Card](https://build.nvidia.com/nvidia/nemotron-3-super-120b-a12b)
- [OpenCode Documentation](https://opencode.ai/docs/)
- [AI SDK Providers](https://ai-sdk.dev/providers/)
- [OpenCode GitHub Repository](https://github.com/anomalyco/opencode)

---

## 🤝 Contributing

Found an issue or have an improvement? Please:
1. Fork this repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

We especially welcome:
- Additional model configurations
- Troubleshooting tips
- Performance benchmarks
- Translation of this guide

---

## ❓ Troubleshooting

**Model not showing in `/models`?**
- Verify your `opencode.json` is valid JSON: `python3 -m json.tool ~/.config/opencode/opencode.json`
- Check that the provider ID matches what you used in `/connect` (run `opencode auth list` to see)
- Restart OpenCode completely after config changes

**Authentication errors?**
- Ensure your NVIDIA API key is correctly stored: `opencode auth list`
- Try regenerating your key at [NVIDIA Build](https://build.nvidia.com/)
- Verify the `baseURL` is exactly `https://integrate.api.nvidia.com/v1`

**Context window not working?**
- Try a prompt with >100k tokens to test the limit
- Check that `limit.context` is set to `1000000` (not `1048576` etc.)
- Some frontends may have their own limits—test in raw OpenCode TUI

**Still stuck?**
- Open an issue in this repository with:
  - Your OpenCode version (`opencode --version`)
  - Your `opencode.json` (with secrets removed)
  - Exact error messages
  - Steps to reproduce

---

## 🏆 About This Repository

This guide is maintained by **open source enthusiasts** who believe in making cutting-edge AI accessible to everyone. As the primary maintainer, I'm dedicated to:
- Keeping this guide updated with the latest model releases
- Providing clear, accurate instructions for developers of all levels
- Building a community around open source AI tooling
- Ensuring every developer can benefit from models like Nemotron 3 Super

**Star this repo** if you found it helpful, and **share it** with fellow developers who want to stay on the cutting edge of AI-assisted development!

---

*Last updated: March 12, 2026*  
*For the brand-new NVIDIA Nemotron 3 Super release*