# Agent Rules

## Python Script Execution

When executing Python scripts, always use `uv run python` instead of directly calling `python` or `python3`.

**Example:**
```bash
# ✅ Correct
uv run python script.py

# ❌ Incorrect
python script.py
python3 script.py
```

This ensures consistent Python environment management and dependency handling across the project.