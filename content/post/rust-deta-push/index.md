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

## Background

[Deta](https://deta.space/) is a platform for building web apps.
Their unique pitch is that each user has a personal cloud.
So a developer could build an app and then the user gets their own instance of the application; Deta handles the separate sign-in and data storage.

A Deta Micro (a service in the app like a web interface) can be built with any technology, so I wanted to experiment with building a web app with Rust.
I built a working app but ran into a problem when pushing to Deta.

The build process on Deta's servers (running on AWS) would timeout before the app was compiled.
So I tried to [cross compile](https://rust-lang.github.io/rustup/cross-compilation.html) the app for Linux on my Mac, but ran into issues that I wasn't able to solve (I'm still very new to the language and processes).

**So my solution was to compile the application on a GitHub Action (GHA) and push the artifact to Deta.**

Below, I provide configuration files for GHA and a Deta application (a "Spacefile") and some comments on their structure.

## The Spacefile

I'll start with the the Spacefile (the configuration for the Deta application) because it is rather simple.
For this use case, the Spacefile doesn't do much other than declare the Micro and tell Deta to execute the Rust artifact when called to run.
Following are more details on the configuration file below.

It contains a single Micro called "my-app" that executes the Rust artifact "my_app" to run.
It starts by declaring the source directory is the current directory (".") and the engine is "custom".
You can see that the `cargo run` command is used for local development (declared in the `dev` field), but this is ignored for deployment.

The fields `include` and `commands` tell Deta what to do for deployment.
You can see that the Rust artifact "my_app" in the current directory is included and the commands don't really do much other than print some information.
This is because the artifact will be built by the GHA and there isn't anything to be done by Deta other than include it in the upload and run it when the Micro is to be executed.

So this Spacefile is rather simple and just needs to be coordinated with the GHA which is covered next.

```yaml
# Spacefile Docs: <https://go.deta.dev/docs/spacefile/v0>
v: 0
micros:
  - name: my-app
    src: .
    engine: custom
    dev: cargo run
    include:
      - ./my_app
    commands:
      - echo "Executable built on GitHub Action"
      - pwd
      - ls -lha
    run: ./my_app
```

## The GitHub Action (GHA)

I have provided an example GHA configuration below and preceded with some description of how it works.

The GHA has two jobs:

1. Compile the app
1. Push to Deta

Because of the latter, the Spacefile configuration needs to be synchronized with the GHA.
How exactly this is accomplished by vary by project and requirements of the system, but below is the simplest scenario of a single artifact to be uploaded and executed by Deta.

There are 5 steps to this job.
The first, fairly standard for GHA, checks out the current project for use in the GHA.

The second action setups up the environment for cross compiling to 64-bit Linux with MUSL.
I'm not certain, but I believe this is necessary because of the specific AWS service that Deta uses.
(I found this specification on the Deta user forum while doing research for this problem.)

The third step build the app for the intended target operating system.

The fourth steps installs the Deta CLI.
The first curl request is just copied from the [Deta installation instructions](https://deta.space/docs/en/build/fundamentals/space-cli).
The following two commands just add the Deta CLI to the path environment variable so it is available to be called and add the Deta Space access token to the environment so it is visible to the Deta CLI when used.
The access token is necessary to authorize the CLI to use your Deta account in the GHA and should be added as a [GitHub secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions).

Finally, the last command links the current process to Deta Space using the `space link` command per the [Deta CLI documentation](https://deta.space/docs/en/build/fundamentals/development/projects#project-linking).
This requires the project ID which should also be stored as a [GitHub secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions).
The build artifact is then copied to the current working directory and the app is pushed to Deta.
Note how this mirrors the expectations of the Spacefile for where the executable will be.

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
          cp ./target/x86_64-unknown-linux-musl/release/my_app ./my_app
          space push
```
