# Mark Buckler's Personal Website

Github repo for my Hugo based personal/academic website. You can see the site [here](https://www.markbuckler.com/).

Many thanks to [Skand Hurkat](https://people.ece.cornell.edu/skand/) for his
[rework of the Hugo Academic Theme](https://github.com/skandhurkat/hugo-theme-cornellcsl).

## Dependencies

This site depends on an old Hugo release and should be built with **Hugo 0.40.1**.

- Required:
  - `hugo` version `0.40.1`
- Helpful:
  - `git`
  - `dpkg-deb` (for extracting the downloaded `.deb` without installing system-wide)

No Node.js, npm, or Python dependencies are required to build this site.

## Install Hugo 0.40.1

First, download the Hugo `0.40.1` Debian package from the official Hugo GitHub releases:

```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.40.1/hugo_0.40.1_Linux-64bit.deb
```

### Option 1: Run locally without system install (recommended)

Extract and run Hugo from a local directory:

```bash
mkdir -p /tmp/hugo0401
dpkg-deb -x ./hugo_0.40.1_Linux-64bit.deb /tmp/hugo0401
/tmp/hugo0401/usr/local/bin/hugo version
```

Expected output includes:

```text
Hugo Static Site Generator v0.40.1
```

### Option 2: Install system-wide on Ubuntu

```bash
sudo dpkg -i hugo_0.40.1_Linux-64bit.deb
hugo version
```

## Build The Website

From the repo root:

```bash
# If using the local extracted binary:
/tmp/hugo0401/usr/local/bin/hugo

# Or, if Hugo 0.40.1 is installed system-wide:
hugo
```

The generated site is written to `docs/` (configured via `publishDir = "./docs"` in `config.toml`).
This repository uses `docs/` for GitHub Pages publishing, so the default build intentionally does not clean the destination directory.

## Run A Local Preview Server

```bash
# Local binary option:
/tmp/hugo0401/usr/local/bin/hugo server --watch

# System-wide install option:
hugo server --watch
```

Then open `http://localhost:1313`.

## Notes

- Newer Hugo versions may fail with template compatibility errors in this theme.
- Avoid `--cleanDestinationDir` unless you intentionally want Hugo to remove files in `docs/` that are not part of the current generated output.
