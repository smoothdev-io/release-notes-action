# SmoothDev Release Notes Action

🤖 AI-powered release notes generation using [SmoothDev](https://smoothdev.io).

## Features

- **AI-Generated Release Notes**: Automatically generate comprehensive release notes between tags
- **GitHub Release Integration**: Optionally create/update GitHub releases directly
- **Multiple Output Formats**: Text, JSON, or Keep a Changelog format
- **Smart Tag Detection**: Auto-detects previous tag if not specified
- **Draft & Prerelease Support**: Create draft or prerelease GitHub releases

## Usage

### Basic Usage

```yaml
name: Release Notes
on:
  push:
    tags:
      - 'v*'

jobs:
  release-notes:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Required for tag history

      - uses: smoothdev-io/release-notes-action@v1
        with:
          api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
```

### Create GitHub Release

```yaml
- uses: smoothdev-io/release-notes-action@v1
  with:
    api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
    push_to_github: true
    release_tag: ${{ github.ref_name }}
```

### Specify Tag Range

```yaml
- uses: smoothdev-io/release-notes-action@v1
  with:
    api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
    from_tag: v1.0.0
    to_tag: v1.1.0
```

### Use Generated Notes in Workflow

```yaml
- uses: smoothdev-io/release-notes-action@v1
  id: notes
  with:
    api_key: ${{ secrets.SMOOTHDEV_API_KEY }}

- name: Use release notes
  run: |
    echo "${{ steps.notes.outputs.release_notes }}"
```

### Changelog Format

```yaml
- uses: smoothdev-io/release-notes-action@v1
  with:
    api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
    output_format: changelog
```

## Inputs

| Input             | Description                                    | Required | Default |
| ----------------- | ---------------------------------------------- | -------- | ------- |
| `api_key`         | SmoothDev API key                              | Yes      | -       |
| `from_tag`        | Starting tag/ref                               | No       | Auto    |
| `to_tag`          | Ending tag/ref                                 | No       | `HEAD`  |
| `output_format`   | Output format: text, json, or changelog        | No       | `text`  |
| `push_to_github`  | Create/update GitHub release                   | No       | `false` |
| `release_tag`     | Release tag (required if push_to_github=true)  | No       | -       |
| `release_name`    | Release name override                          | No       | -       |
| `draft`           | Create as draft release                        | No       | `false` |
| `prerelease`      | Mark as prerelease                             | No       | `false` |
| `use_ai_gateway`  | Use AI Gateway instead of REST API             | No       | `false` |
| `cli_version`     | Specific smooth-cli version to install         | No       | Latest  |

## Outputs

| Output          | Description              |
| --------------- | ------------------------ |
| `release_notes` | Generated release notes  |

## Setup

### 1. Get a SmoothDev API Key

Sign up at [smoothdev.io](https://smoothdev.io) and generate an API key from your dashboard.

### 2. Add Secret to Repository

Add your API key as a repository secret:

1. Go to your repository → Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `SMOOTHDEV_API_KEY`
4. Value: Your SmoothDev API key

### 3. Add Workflow

Create `.github/workflows/release.yml` with the usage example above.

## Permissions

This action requires the following permissions:

```yaml
permissions:
  contents: write  # Required if push_to_github is true
```

## Examples

### Full Release Workflow

```yaml
name: Release
on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: smoothdev-io/release-notes-action@v1
        with:
          api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
          push_to_github: true
          release_tag: ${{ github.ref_name }}
          release_name: "Release ${{ github.ref_name }}"
```

### Draft Release for Review

```yaml
- uses: smoothdev-io/release-notes-action@v1
  with:
    api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
    push_to_github: true
    release_tag: ${{ github.ref_name }}
    draft: true
```

### Prerelease for Beta/RC Tags

```yaml
- uses: smoothdev-io/release-notes-action@v1
  with:
    api_key: ${{ secrets.SMOOTHDEV_API_KEY }}
    push_to_github: true
    release_tag: ${{ github.ref_name }}
    prerelease: ${{ contains(github.ref_name, '-beta') || contains(github.ref_name, '-rc') }}
```

## License

Apache License 2.0 - see [LICENSE](LICENSE) for details.

## Links

- [SmoothDev](https://smoothdev.io)
- [Documentation](https://docs.smoothdev.io)
- [CLI Repository](https://github.com/smoothdev-io/sd-smooth-cli)
- [PR Summary Action](https://github.com/smoothdev-io/pr-summary-action)
