# Oh My Zsh Role

An Ansible role that installs and configures Oh My Zsh, a popular framework for managing ZSH configuration with themes, plugins, and helpful features.

## Description

This role sets up Oh My Zsh framework by:

- Installing git (required dependency for Oh My Zsh)
- Cloning the Oh My Zsh repository to the user's home directory
- Creating a configuration snippet that integrates with the `.zshrc.d/` structure
- Supporting customizable themes and plugins
- Maintaining compatibility with modular ZSH configuration approaches

The role creates a configuration snippet that is designed to work with the `zshconfig` role's modular approach, placing the Oh My Zsh initialization in the `.zshrc.d/ansible-managed/` directory.

## Features

- **Automatic Installation**: Downloads and installs Oh My Zsh from the official repository
- **Modular Integration**: Works seamlessly with `.zshrc.d/` directory structure
- **Theme Support**: Configurable ZSH themes through Oh My Zsh
- **Plugin Management**: Easy configuration of Oh My Zsh plugins
- **Git Dependency**: Ensures git is installed as a prerequisite
- **Version Control**: Keeps Oh My Zsh updated to the latest version

## Requirements

- ZSH shell installed on the target system
- Internet connection for downloading Oh My Zsh repository
- Ansible 2.9 or higher
- Target user should have appropriate permissions for file operations
- Works best when used with the `zshconfig` role for complete shell setup

## Role Variables

### User-Configurable Variables

The following variables can be set by users to customize the role behavior:

#### Theme Configuration

```yaml
# Oh My Zsh theme to use (optional)
# If not defined, Oh My Zsh will use its default theme
ohmyzsh_theme: "robbyrussell" # Examples: "agnoster", "powerlevel10k/powerlevel10k", "random"
```

#### Plugin Configuration

```yaml
# List of Oh My Zsh plugins to enable (optional)
# If not defined, no plugins will be explicitly loaded
ohmyzsh_plugins:
  - git
  - docker
  - kubectl
  - brew
  - macos
  - vscode
```

### Internal Variables

These variables are used internally by the role and typically don't need to be modified:

```yaml
# Directory where Oh My Zsh will be installed
ohmyzsh_install_dir: "{{ ansible_user_dir }}/.oh-my-zsh"

# Git repository URL for Oh My Zsh
ohmyzsh_repo: "https://github.com/ohmyzsh/ohmyzsh.git"

# Directory where the Oh My Zsh snippet will be placed
ohmyzsh_d_dir: "{{ ansible_user_dir }}/.zshrc.d/ansible-managed"

# Filename for the Oh My Zsh configuration snippet
ohmyzsh_snippet_filename: "20-oh-my-zsh.zsh"
```

## Configuration Files Created

The role creates the following files and directories:

### Core Installation

- `~/.oh-my-zsh/` - Main Oh My Zsh installation directory
- `~/.zshrc.d/ansible-managed/20-oh-my-zsh.zsh` - Configuration snippet for Oh My Zsh integration

### Oh My Zsh Structure

The Oh My Zsh installation includes:

- Themes in `~/.oh-my-zsh/themes/`
- Plugins in `~/.oh-my-zsh/plugins/`
- Custom configurations in `~/.oh-my-zsh/custom/`
- Core Oh My Zsh files and libraries

## Usage Examples

### Basic Installation

```yaml
- name: Install Oh My Zsh with defaults
  include_role:
    name: ohmyzsh
```

### With Custom Theme

```yaml
- name: Install Oh My Zsh with Agnoster theme
  include_role:
    name: ohmyzsh
  vars:
    ohmyzsh_theme: "agnoster"
```

### With Plugins

```yaml
- name: Install Oh My Zsh with useful plugins
  include_role:
    name: ohmyzsh
  vars:
    ohmyzsh_plugins:
      - git
      - docker
      - kubectl
      - brew
      - macos
      - history-substring-search
```

### Complete Setup with Theme and Plugins

```yaml
- name: Full Oh My Zsh setup
  include_role:
    name: ohmyzsh
  vars:
    ohmyzsh_theme: "powerlevel10k/powerlevel10k"
    ohmyzsh_plugins:
      - git
      - docker
      - kubectl
      - brew
      - macos
      - vscode
      - history-substring-search
      - zsh-autosuggestions
      - zsh-syntax-highlighting
```

### Combined with ZSH Configuration

```yaml
- name: Complete ZSH setup with Oh My Zsh
  block:
    - name: Configure ZSH shell
      include_role:
        name: zshconfig
      vars:
        zshconfig_set_default_shell: true
        configure_zshconfig_preserve_files:
          - "20-oh-my-zsh.zsh"

    - name: Install and configure Oh My Zsh
      include_role:
        name: ohmyzsh
      vars:
        ohmyzsh_theme: "robbyrussell"
        ohmyzsh_plugins:
          - git
          - brew
          - macos
```

## Popular Themes

Here are some popular Oh My Zsh themes you can use:

- `robbyrussell` - Default, clean and simple
- `agnoster` - Popular theme with git status and virtualenv info
- `powerlevel10k/powerlevel10k` - Highly customizable and fast theme
- `bira` - Two-line theme with time and git info
- `fino` - Clean theme with git and time info
- `random` - Randomly selects a theme each time

## Popular Plugins

Common Oh My Zsh plugins that enhance productivity:

- `git` - Git aliases and functions
- `docker` - Docker completion and aliases
- `kubectl` - Kubernetes command completion
- `brew` - Homebrew completion (macOS)
- `macos` - macOS specific functions and aliases
- `vscode` - Visual Studio Code integration
- `history-substring-search` - Fish-like history search
- `zsh-autosuggestions` - Fish-like autosuggestions
- `zsh-syntax-highlighting` - Real-time syntax highlighting

## Integration with Other Roles

This role is designed to work seamlessly with the `zshconfig` role:

1. Run `zshconfig` first to set up the modular ZSH structure
2. Configure `zshconfig` to preserve the Oh My Zsh snippet file
3. Run `ohmyzsh` to install and configure Oh My Zsh
4. The Oh My Zsh configuration will be automatically loaded through the `.zshrc.d/` structure

## Customization

### Adding Custom Plugins

You can add custom plugins by:

1. **Using the plugins variable**: Add plugin names to the `ohmyzsh_plugins` list
2. **Installing external plugins**: Some plugins need to be installed separately (like zsh-autosuggestions)
3. **Custom plugins**: Place custom plugins in `~/.oh-my-zsh/custom/plugins/`

### Theme Customization

- **Built-in themes**: Use any theme from `~/.oh-my-zsh/themes/`
- **External themes**: Install themes to `~/.oh-my-zsh/custom/themes/`
- **Theme configuration**: Some themes have additional configuration options

### Load Order

When used with the `zshconfig` role, the Oh My Zsh configuration loads as:

1. ZSH configuration fragments in `~/.zshrc.d/ansible-managed/` (alphabetically)
2. Oh My Zsh loads via `20-oh-my-zsh.zsh` (naming ensures proper order)
3. User configurations in `~/.zshrc.d/` (can override Oh My Zsh settings)

## Dependencies

- **git**: Should be installed before running this role.
- **zsh**: Should be installed and configured (preferably via `zshconfig` role)

## Compatibility

- **Operating Systems**: Linux, macOS, WSL
- **Shell**: ZSH
- **Ansible**: 2.9+
- **Oh My Zsh**: Latest version from master branch

## Troubleshooting

### Common Issues

1. **Permission errors**: Ensure the target user has write permissions to their home directory
2. **Git not found**: Ensure git is installed.
3. **Theme not found**: Verify theme name spelling and availability
4. **Plugin errors**: Check that plugin names are correct and supported

### Updates

The role automatically updates Oh My Zsh to the latest version on each run. Alternately, you can run `git pull` inside `"{{ ansible_user_dir }}/.oh-my-zsh"`.
