# For most projects, this workflow file will not need changing; you simply need
# to commit it to your repository.
#
# You may wish to alter this file to override the set of languages analyzed,
# or to provide custom queries or build logic.
#
# ******** NOTE ********
# We have attempted to detect the languages in your repository. Please check
# the `language` matrix defined below to confirm you have the correct set of
# supported CodeQL languages.
#
name: "CodeQL"

on:
  push:
    branches: [ master, stable* ]
  pull_request:
    # The branches below must be a subset of the branches above
    branches: [ master, stable* ]
  schedule:
    - cron: '30 8 * * 6'
    
permissions:
  contents: read

jobs:
  analyze:
    permissions:
      security-events: write
    name: Analyze
    runs-on: ubuntu-latest

    strategy:
      fail-fast: false
      matrix:
        language: [ 'cpp' ]
        # CodeQL supports [ 'cpp', 'csharp', 'go', 'java', 'javascript', 'python' ]
        # Learn more:
        # https://docs.github.com/en/free-pro-team@latest/github/finding-security-vulnerabilities-and-errors-in-your-code/configuring-code-scanning#changing-the-languages-that-are-analyzed

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    # Initializes the CodeQL tools for scanning.
    - name: Initialize CodeQL
      uses: github/codeql-action/init@v2
      with:
        languages: ${{ matrix.language }}
        # If you wish to specify custom queries, you can do so here or in a config file.
        # By default, queries listed here will override any specified in a config file.
        # Prefix the list here with "+" to use these queries and those in the config file.
        # queries: ./path/to/local/query, your-org/your-repo/queries@main

    # Autobuild attempts to build any compiled languages  (C/C++, C#, or Java).
    # If this step fails, then you should remove it and run the build manually (see below)
    # - name: Autobuild
    #   uses: github/codeql-action/autobuild@v2

    # ℹ️ Command-line programs to run using the OS shell.
    # 📚 https://git.io/JvXDl

    # ✏️ If the Autobuild fails above, remove it and uncomment the following three lines
    #    and modify them (or add more) to build your code if your project
    #    uses a compiled language

    - run: |
       sudo apt update
       sudo apt install libxrandr-dev libxinerama-dev libusb-1.0-0-dev xserver-xorg-dev libswscale-dev libswresample-dev libavutil-dev libavcodec-dev libcups2-dev libpulse-dev libasound2-dev libpcsclite-dev xsltproc libxcb-cursor-dev libxcursor-dev libcairo2-dev libfaac-dev libfaad-dev libjpeg-dev libgsm1-dev ninja-build libxfixes-dev libxkbcommon-dev libwayland-dev libpam0g-dev libxdamage-dev libxcb-damage0-dev ccache libxtst-dev libfuse3-dev libsystemd-dev libcairo2-dev libsoxr-dev libsdl2-dev docbook-xsl libkrb5-dev libcjson-dev libpkcs11-helper1-dev libsdl2-ttf-dev libwebkit2gtk-4.0-dev clang
       mkdir ci-build
       cd ci-build
       export CC=/usr/bin/clang
       export CXX=/usr/bin/clang++
       export CFLAGS="-Weverything"
       export CXXFLAGS="-Weverything"
       cmake -GNinja ../ci/cmake-preloads/config-linux-all.txt ..
       cmake --build .

    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v2￼Enter
