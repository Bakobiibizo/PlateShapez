# 📜 PlateShapez

A research tool for generating adversarially perturbed license plate overlays on vehicle images, producing structured datasets with reproducibility, transparency, and ethical guardrails.

**Design Principle:** *user-first, safe by default, hackable by experts*.

## 🚀 Quick Start

### Prerequisites

- **uv** (Python package manager) installed:
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

### Installation

```bash
git clone https://github.com/benjordan/plateshapez.git
cd plateshapez

# Install dependencies
uv sync --group dev

# Install the CLI tool
uv pip install -e .
```

### Basic Usage

1. **Prepare your data:**
   ```
   project/
   ├── backgrounds/     # Vehicle images (JPG)
   ├── overlays/        # License plate images (PNG with alpha)
   └── config.yaml      # Optional configuration
   ```

2. **Generate dataset:**
   ```bash
   # Using CLI
   advplate generate --n_variants 10

   # With custom config
   advplate generate --config config.yaml

   # Preview without generating files
   advplate generate --dry-run
   ```

3. **Explore options:**
   ```bash
   # List available perturbations
   advplate list

   # Show current configuration
   advplate info

   # Show version
   advplate version
   ```

### Python API

```python
from plateshapez import DatasetGenerator

# Generate dataset programmatically
gen = DatasetGenerator(
    bg_dir="backgrounds",
    overlay_dir="overlays",
    out_dir="dataset",
    perturbations=[
        {"name": "shapes", "params": {"num_shapes": 20}},
        {"name": "noise", "params": {"intensity": 25}}
    ],
    random_seed=1337
)
gen.run(n_variants=10)
```

## 🎛️ Configuration

### Configuration File (config.yaml)

```yaml
dataset:
  backgrounds: "./backgrounds"
  overlays: "./overlays"
  output: "./dataset"
  n_variants: 10
  random_seed: 1337

perturbations:
  - name: shapes
    params:
      num_shapes: 20
      min_size: 2
      max_size: 15
  - name: noise
    params:
      intensity: 25
  - name: texture
    params:
      type: grain
      intensity: 0.3
  - name: warp
    params:
      intensity: 5.0
      frequency: 20.0

logging:
  level: INFO
  save_metadata: true
```

### Available Perturbations

- **shapes**: Random rectangles, ellipses, triangles
- **noise**: Add Gaussian or salt noise  
- **warp**: Mild geometric warping
- **texture**: Overlay texture maps (grain, scratches, dirt)

## 📁 Output Structure

Generated datasets follow this structure:

```
dataset/
├── images/              # Generated composite images
│   ├── car1_plate1_000.png
│   ├── car1_plate1_001.png
│   └── ...
└── labels/              # Metadata JSON files
    ├── car1_plate1_000.json
    ├── car1_plate1_001.json
    └── ...
```

Each JSON file contains:
- Background and overlay filenames
- Overlay position and size
- Applied perturbations with parameters
- Random seed for reproducibility

## 🔧 Development (npm-like commands)

You can use either the console script (after `uv sync`) or the Bash wrapper.

- Console script (requires one-time `uv sync`):
  ```bash
  # Format / Lint / Type-check / All checks
  uv run dev format
  uv run dev lint
  uv run dev type
  uv run dev check

  # Pre-commit hooks
  uv run dev hooks install   # installs pre-commit & pre-push hooks
  uv run dev pre-commit      # run hooks on all files
  ```

- Bash wrapper (works without installing the package):
  ```bash
  ./scripts/dev format
  ./scripts/dev lint
  ./scripts/dev type
  ./scripts/dev check
  ./scripts/dev hooks install
  ./scripts/dev pre-commit
  ```

These map to the same underlying tools and are aligned with CI and `scripts/check.sh`.

## What the commands do

- `format`: `ruff format .`
- `lint`: `ruff check . --fix`
- `type`: `mypy .`
- `check`: runs format, lint, and type in sequence (same as `scripts/check.sh`)
- `hooks install`: `pre-commit install --hook-type pre-commit --hook-type pre-push`
- `pre-commit`: `pre-commit run --all-files`

## CI

GitHub Actions runs the same checks via `./scripts/check.sh`. Local pre-commit hooks use the same tools via uv to avoid version drift.
