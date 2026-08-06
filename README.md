{
	// See https://go.microsoft.com/fwlink/?LinkId=827846 to learn about workspace recommendations.
	// Extension identifier format: ${publisher}.${name}. Example: vscode.csharp
	// List of extensions which should be recommended for users of this workspace.
	"recommendations": [
		"ms-python.black-formatter",
		"llvm-vs-code-extensions.vscode-clangd",
		"amiralizadeh9480.cpp-helper",
		"marus25.cortex-debug",
		"zxh404.vscode-proto3",
		"augustocdias.tasks-shell-input"
	],
	// List of extensions recommended by VS Code that should not be recommended for users of this workspace.
	"unwantedRecommendations": [
		"twxs.cmake",
		"ms-vscode.cpptools",
		"ms-vscode.cmake-tools"
	]
}

  name: "Build for multiple SDK sources"

on:
  push:
    branches:
      - master
  pull_request:
  schedule:
    # do a build every day
    - cron: "1 1 * * *"

jobs:
  ufbt-build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        app-dir:
          - bthome
        sdk-channel:
          - dev
          - release

    name: 'Build ${{ matrix.app-dir }} for ${{ matrix.sdk-channel }}'
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build with ufbt
        uses: flipperdevices/flipperzero-ufbt-action@v0.1
        id: build-app
        with:
          app-dir: ${{ matrix.app-dir }}
          sdk-channel: ${{ matrix.sdk-channel }}

      - name: Upload app artifacts
        uses: actions/upload-artifact@v4
        with:
          name: ${{ matrix.app-dir }}-${{ steps.build-app.outputs.suffix }}
          path: ${{ steps.build-app.outputs.fap-artifacts }}
