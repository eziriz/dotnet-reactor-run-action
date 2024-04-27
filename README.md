
# .NET Reactor Run Action

This is the .NET Reactor Run Action. It allows you to run .NET Reactor after it has been installed. Make sure you have already installed .NET Reactor using the .NET Reactor Install Action.

## Action 1: 'Install .NET Reactor'

This action downloads and installs .NET Reactor based on the operating system of the GitHub Actions runner. If no specific version is specified, the latest version will be installed. If a license is not specified, .NET Reactor runs in demo mode.

### Inputs

- `version`: (Optional) Specify the exact version of .NET Reactor to install, including major, minor, build, and revision numbers (e.g., 6.9.8.0). If unspecified, the latest version will be used.
- `license`: (Optional) Provide the .NET Reactor license as either a file path or a base64-encoded string. Without a license, .NET Reactor will run in demo mode.

### Example Usage

```yaml
steps:
- name: Install .NET Reactor
  uses: eziriz/dotnet-reactor-install-action@v1.0.0
  with:
    version: '6.9.8.0'
    license: ${{ secrets.NET_REACTOR_LICENSE }}
```

## Action 2: 'Run .NET Reactor'

This action runs .NET Reactor to obfuscate .NET assemblies. It uses the configuration defined in the 'Install .NET Reactor' action. If the 'Install .NET Reactor' action is not used, it falls back to the latest demo version.

### Inputs

- `project_file`: (Optional) Path to the .NET Reactor project file.
- `input_path`: (Optional) Input path of the .NET assembly to obfuscate.
- `output_path`: (Optional) Output path for the obfuscated .NET assembly.
- `additional_arguments`: (Optional) Additional command-line parameters for .NET Reactor. Use **-licensed** to ensure this action fails with error code 101 if .NET Reactor is not fully licensed. If .NET Reactor doesn't run as full version the protected software will stop working after 14 days. This can help identify licensing issues early in the pipeline.

### Example Usage

```yaml
steps:
- name: Run .NET Reactor
  uses: eziriz/dotnet-reactor-run-action@v1.0.0
  with:
    input_path: '$GITHUB_WORKSPACE\path\to\assembly.dll'
    output_path: '$GITHUB_WORKSPACE\path\to\obfuscated\<AssemblyFileName>'
    additional_arguments: '-licensed'
```

## Full Workflow Example

Here's a full example of a workflow that installs and runs .NET Reactor:

```yaml
name: .NET Assembly Obfuscation

on: [push]

jobs:
  obfuscate:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Install .NET Reactor
      uses: eziriz/dotnet-reactor-install-action@v1.0.0
      with:
        license: ${{ secrets.NET_REACTOR_LICENSE }}

    - name: Build Assembly
      run: dotnet build -c Release

    - name: Run .NET Reactor
      uses: eziriz/dotnet-reactor-run-action@v1.0.0
      with:
        input_path: '$GITHUB_WORKSPACE\bin\Release\myapp.dll'
        output_path: '$GITHUB_WORKSPACE\bin\Release\obfuscated\<AssemblyFileName>'
        additional_arguments: '-licensed'
```

If you're feeling motivated, come visit our [website](https://www.eziriz.com/).