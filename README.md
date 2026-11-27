# ContribArt

Generate customizable SVG artwork from your GitHub contribution graph.

ContribArt renders an animated 53×7 contribution-grid SVG displaying custom text, ready to embed directly into your GitHub Profile `README.md` with no promotional footer or external links.

## Features

- Generates clean, animated SVG artwork in GitHub contribution-graph style.
- Customizable text rendered across the grid (up to 8 characters).
- Optional custom 5-color HEX palette (`--c0` through `--c4`) with strict HEX validation.
- Preserves the classic GitHub green palette when no custom colors are provided.
- Configurable output SVG filename (`svg_file_name`).
- Backward-compatible GitHub Action inputs and workflow conventions.

## Generated Artwork Preview

![ContribArt](gitartwork.svg)

## Requirements

- A GitHub repository (such as your GitHub profile repository).
- GitHub Actions enabled with **Read and write permissions** under **Settings > Actions > General > Workflow permissions** so the generated SVG can be committed and pushed back to your repository.

## GitHub Action Setup

1. Open your repository on GitHub and go to **Settings > Actions > General > Workflow permissions**.
2. Select **Read and write permissions** and save.
3. Create `.github/workflows/contribart.yml` in your repository using the workflow configuration below.
4. Embed the generated SVG in your `README.md`:

```markdown
![ContribArt](gitartwork.svg)
```

## Workflow Configuration

```yaml
name: Generate ContribArt SVG

on:
  push:
  workflow_dispatch:

jobs:
  build:
    name: Make ContribArt SVG
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: hasheramin5-cyber/ContribArt@v1
        with:
          user_name: hasheramin5-cyber
          text: Hasher
      - name: Commit and push generated SVG
        run: |
          git config --local user.email "github-actions[bot]@users.noreply.github.com"
          git config --local user.name "github-actions[bot]"
          git add -A
          git diff --cached --quiet || (git commit -m "Update ContribArt SVG" && git push)
```

### Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `user_name` | No | `hasheramin5-cyber` | GitHub username |
| `text` | Yes | `Hasher` | Text displayed on the contribution grid (up to 8 characters) |
| `svg_file_name` | No | `gitartwork.svg` | Output SVG file path |
| `colors` | No | `""` | Comma-separated list of 5 HEX colors for levels 0–4 |

## Custom Color Configuration

You can customize the five contribution intensity levels (`--c0` through `--c4`) by passing a comma-separated list of 5 valid HEX colors to the `colors` input:

```yaml
- uses: hasheramin5-cyber/ContribArt@v1
  with:
    user_name: hasheramin5-cyber
    text: Hasher
    colors: "#0D1117,#0E4429,#006D32,#26A641,#39D353"
```

- Exactly **5** comma-separated HEX colors are required when `colors` is specified (`#RGB`, `#RGBA`, `#RRGGBB`, or `#RRGGBBAA`).
- Level `0` (first color) styles the background/inactive cells and cell outlines; levels `1`–`4` style the animated text cells.
- Invalid color strings or palettes with fewer/more than 5 colors are rejected with a clear workflow error.

## Default Color Behavior

When `colors` is omitted or empty, ContribArt uses the default GitHub-style palette:

- Level 0: `rgba(27, 31, 35, 0.06)`
- Level 1: `#9be9a8`
- Level 2: `#40c463`
- Level 3: `#30a14e`
- Level 4: `#216e39`

## Troubleshooting

- **`Permission to ... denied to github-actions[bot]`**: Ensure **Read and write permissions** is enabled under **Settings > Actions > General > Workflow permissions**.
- **`Invalid colors input` error**: Verify that `colors` contains exactly 5 comma-separated HEX values starting with `#` (e.g. `"#0D1117,#0E4429,#006D32,#26A641,#39D353"`).
- **Text truncated**: Only the first 8 characters of `text` are rendered on the 53-column grid.

## License & Attribution

Licensed under the [BSD-3-Clause License](LICENSE.md).

Maintained by **Hasher Amin** (`hasheramin5-cyber`).

ContribArt is derived from [gitartwork](https://github.com/jasineri/gitartwork) by Nerijus Jasinskas (`Copyright (c) 2022, Nerijus Jasinskas`), with the original BSD-3-Clause copyright notice and license terms preserved.
