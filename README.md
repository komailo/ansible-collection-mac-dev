# Ansible Collection: komailo.mac_dev

[![License](https://img.shields.io/badge/license-BSD--3--Clause-blue.svg)](LICENSE)
[![Ansible](https://img.shields.io/badge/ansible-%3E%3D2.9.10-black.svg)](https://github.com/ansible/ansible)

A comprehensive Ansible collection for setting up and configuring Mac-based development environments. This collection automates the complete developer workflow setup on macOS, including development tools installation, dotfiles configuration, shell customization, and more.

## Features

- 🔧 **Modular Configuration**: Clean separation between Ansible-managed and user-defined settings
- 🐚 **Shell Customization**: ZSH configuration with Oh My Zsh support
- 📝 **Git Configuration**: Comprehensive Git setup with signing, aliases, and custom configurations
- 🍺 **Package Management**: Homebrew integration for development tools
- 🎯 **Developer Focused**: Tailored specifically for macOS development environments
- 🔄 **Idempotent**: Safe to run multiple times without unwanted side effects

## Requirements

- **Operating System**: macOS (tested on macOS 10.15+)
- **Ansible**: >= 2.9.10
- **Python**: >= 3.6
- **Privileges**: Some roles may require sudo access for system-level changes

## Installation

### Via Ansible Galaxy

```bash
ansible-galaxy collection install komailo.mac_dev
```

### Via requirements.yml

Create a `requirements.yml` file:

```yaml
---
collections:
  - name: komailo.mac_dev
    version: ">=0.1.0"
```

Then install:

```bash
ansible-galaxy collection install -r requirements.yml
```

### From Source

```bash
git clone https://github.com/your-username/ansible-collection-mac-dev.git
cd ansible-collection-mac-dev
ansible-galaxy collection build
ansible-galaxy collection install komailo-mac_dev-*.tar.gz
```

## Roles

### gitconfig

Manages Git configuration with modular approach using `.gitconfig.d/` directory structure.

**Key Features:**

- User identity configuration (name and email)
- GPG/SSH signing setup
- Git aliases for improved productivity
- Custom configuration support
- Separation between managed and user-defined settings

**Usage:**

```yaml
- name: Configure Git
  include_role:
    name: komailo.mac_dev.gitconfig
  vars:
    git_user_name: "Your Name"
    git_user_email: "your.email@example.com"
    git_signing_key: "your-gpg-key-id"
```

### ohmyzsh

Installs and configures Oh My Zsh framework for enhanced ZSH experience.

**Key Features:**

- Oh My Zsh installation and setup
- Theme and plugin configuration
- Integration with modular ZSH configuration
- Customizable themes and plugins

**Usage:**

```yaml
- name: Setup Oh My Zsh
  include_role:
    name: komailo.mac_dev.ohmyzsh
  vars:
    ohmyzsh_theme: "robbyrussell"
    ohmyzsh_plugins:
      - git
      - brew
      - docker
```

### zshconfig

Comprehensive ZSH shell configuration with modular structure.

**Key Features:**

- ZSH as default shell setup
- Modular configuration using `.zshrc.d/` directory
- Common shell utilities and aliases
- GitHub CLI integration
- Environment variable management

**Usage:**

```yaml
- name: Configure ZSH
  include_role:
    name: komailo.mac_dev.zshconfig
  vars:
    zsh_aliases:
      - { alias: "ll", command: "ls -la" }
      - { alias: "la", command: "ls -A" }
```

## Quick Start

### 1. Create a Playbook

Create a playbook file `setup-mac-dev.yml`:

```yaml
---
- name: Setup Mac Development Environment
  hosts: localhost
  connection: local
  gather_facts: yes

  vars:
    git_user_name: "Your Name"
    git_user_email: "your.email@example.com"
    ohmyzsh_theme: "agnoster"
    ohmyzsh_plugins:
      - git
      - brew
      - docker
      - kubectl

  tasks:
    - name: Ensure we're running on macOS
      assert:
        that:
          - ansible_os_family == "Darwin"
        fail_msg: "This playbook is designed for macOS only"

    - name: Configure Git
      include_role:
        name: komailo.mac_dev.gitconfig

    - name: Setup Oh My Zsh
      include_role:
        name: komailo.mac_dev.ohmyzsh

    - name: Configure ZSH
      include_role:
        name: komailo.mac_dev.zshconfig
```

### 2. Run the Playbook

```bash
ansible-playbook -i localhost, setup-mac-dev.yml
```

## Examples

Check the `examples/` directory for more comprehensive playbook examples:

- [`setup-mac-dev.yml`](examples/setup-mac-dev.yml) - Complete development environment setup

## Configuration

### Directory Structure

This collection creates a modular configuration structure:

```text
~/.gitconfig.d/
├── ansible-managed/    # Managed by Ansible
│   └── *.gitconfig
└── user-defined/       # Your custom configurations
    └── *.gitconfig

~/.zshrc.d/
├── ansible-managed/    # Managed by Ansible
│   ├── aliases.zsh
│   ├── functions.zsh
│   └── environment.zsh
└── user-defined/       # Your custom configurations
    └── *.zsh
```

### Variable Precedence

Variables can be set at multiple levels:

1. Role defaults (lowest priority)
2. Inventory variables
3. Playbook variables
4. Extra variables (highest priority)

## Advanced Usage

### Custom Git Configuration

```yaml
- name: Configure Git with signing
  include_role:
    name: komailo.mac_dev.gitconfig
  vars:
    git_user_name: "Developer Name"
    git_user_email: "dev@company.com"
    git_signing_key: "ABCD1234"
    git_signing_format: "ssh" # or "gpg"
    git_custom_configs:
      - section: "core"
        option: "editor"
        value: "code --wait"
      - section: "push"
        option: "default"
        value: "simple"
```

### ZSH with Custom Functions

```yaml
- name: Configure ZSH with custom functions
  include_role:
    name: komailo.mac_dev.zshconfig
  vars:
    zsh_custom_functions:
      - name: "mkcd"
        definition: |
          mkcd() {
            mkdir -p "$1" && cd "$1"
          }
    zsh_environment_vars:
      - name: "EDITOR"
        value: "code"
      - name: "BROWSER"
        value: "open"
```

## Testing

The collection includes comprehensive testing:

```bash
# Run all tests
ansible-test integration

# Run specific role tests
ansible-test integration gitconfig
ansible-test integration ohmyzsh
ansible-test integration zshconfig
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Setup

```bash
git clone https://github.com/your-username/ansible-collection-mac-dev.git
cd ansible-collection-mac-dev

# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
ansible-test integration --docker
```

## Changelog

See [CHANGELOG.md](changelogs/changelog.yaml) for a detailed history of changes.

## License

This project is licensed under the BSD 3-Clause License - see the [LICENSE](LICENSE) file for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/your-username/ansible-collection-mac-dev/issues)
- **Documentation**: [Project Wiki](https://github.com/your-username/ansible-collection-mac-dev/wiki)
- **Discussions**: [GitHub Discussions](https://github.com/your-username/ansible-collection-mac-dev/discussions)

## Author

**Komail Kanjee** - [hey@komail.io](mailto:hey@komail.io)

---

⭐ If this collection helped you, please consider giving it a star on GitHub!
