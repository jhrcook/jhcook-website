---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Build and push a Rust project to Deta on GitHub Actions"
subtitle: "Using GitHub actions to cross-compile and push a Rust project to Deta."
summary: "Pushing Rust projects to Deta can require cross-compiling or timeout during the build process. Compiling on a Linux runner on GitHub Actions and pushing the artifact can solve both of these issues."
authors: []
tags: ["GitHub Actions", "Rust", "Deta"]
categories: [Programming]
date: 2023-09-23T13:49:58-04:00
lastmod: 2023-09-23T13:49:58-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: true

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

The goal.

The problem.

The solution.

GitHub Action YAML: ".github/"

```yaml
name: deta-build

run-name: Build and push to Deta Space

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: GitHub Actions checkout
        uses: actions/checkout@v3
      - name: Cross-link toolchain
        uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
          target: x86_64-unknown-linux-musl
          override: true
      - name: Build workspace
        uses: actions-rs/cargo@v1
        with:
          use-cross: true
          command: build
          args: --release --target x86_64-unknown-linux-musl
      - name: Install Deta Space CLI and link to project
        shell: bash
        run: |
          curl -fsSL https://get.deta.dev/space-cli.sh | sh
          echo "$HOME/.detaspace/bin" >> $GITHUB_PATH
          echo "SPACE_ACCESS_TOKEN=${{ secrets.ACCESS_TOKEN }}" >> $GITHUB_ENV
      - name: Push to Deta Space
        shell: bash
        run: |
          space link --id "${{ secrets.PROJECT_ID }}"
          cp ./target/x86_64-unknown-linux-musl/release/trout_scraping_server trout_scraping_server_app
          echo "DJANGO_SECRET_KEY=${{ secrets.DJANGO_SECRET_KEY }}" > frontend/secrets.txt
          space push
```

Spacefile:

```yaml
# Spacefile Docs: <https://go.deta.dev/docs/spacefile/v0>

v: 0
micros:
  - name: data-scraping
    src: .
    engine: custom
    dev: cargo run
    include:
      - ./trout_scraping_server_app
    commands:
      - echo "Executable built on GitHub Action"
      - pwd
      - ls -lha
    presets:
      env:
        - name: RUST_LOG
          description: Secret message only available to this Micro
          default: "info"

    run: ./trout_scraping_server_app
```
