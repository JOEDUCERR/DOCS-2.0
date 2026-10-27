### Old Config
```
{
  "$schema": "https://opencode.ai/config.json",
  "model": "qwen27b/qwen3.8-27b",
  "small_model": "qwen8b/qwen3-8b",
  "provider": {
    "qwen27b": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Techlead AI - Qwen3.8-27B",
      "options": {
        "baseURL": "http://20.20.20.152:8000/v1",
        "timeout": 300000
      },
      "models": {
        "qwen3.8-27b": {
          "name": "Qwen3.8-27B Q4_K_M",
          "limit": {
            "context": 70000,
            "output": 8192
          }
        }
      }
    },
    "qwen8b": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Techlead AI - Qwen3-8B",
      "options": {
        "baseURL": "http://20.20.20.152:8001/v1",
        "timeout": 300000
      },
      "models": {
        "qwen3-8b": {
          "name": "Qwen3-8B Q4_K_M",
          "limit": {
            "context": 16384,
            "output": 4096
          }
        }
      }
    }
  }
}

```

### New Config
```
{
  "$schema": "https://opencode.ai/config.json",
  "model": "qwen27b/qwen3.8-27b",
  "small_model": "qwen27b/qwen3.8-27b",
  "provider": {
    "qwen27b": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Techlead AI - Qwen3.8-27B",
      "options": {
        "baseURL": "http://20.20.20.152:8000/v1",
        "timeout": 1800000
      },
      "models": {
        "qwen3.8-27b": {
          "name": "Qwen3.8-27B Q4_K_M",
          "limit": {
            "context": 65536,
            "output": 16384
          }
        }
      }
    },
    "qwen8b": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Techlead AI - Qwen3-8B",
      "options": {
        "baseURL": "http://20.20.20.152:8001/v1",
        "timeout": 1800000
      },
      "models": {
        "qwen3-8b": {
          "name": "Qwen3-8B Q4_K_M",
          "limit": {
            "context": 32768,
            "output": 8192
          }
        }
      }
    }
  }
}
```