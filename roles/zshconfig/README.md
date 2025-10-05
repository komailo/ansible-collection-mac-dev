# ZSH Configuration Role

An Ansible role that manages ZSH shell configuration for a user, providing a modular and extensible approach to shell customization.

## Description

This role configures the ZSH shell environment by:

- Setting ZSH as the default shell for the user
- Creating a modular configuration structure using `.zshrc.d/` directory
- Managing configuration files through Ansible templates
- Providing common shell utilities and configurations
- Supporting GitHub CLI integration
- Managing shell aliases and functions

The role creates a `.zshrc` file that sources configuration fragments from both Ansible-managed and user-defined directories, allowing for clean separation between automated and manual configurations.

## Features

- **Modular Configuration**: Uses `.zshrc.d/` directory structure for organized configuration management
- **Ansible-Managed Files**: Separates automated configurations from user customizations
- **GitHub CLI Integration**: Optional automatic GitHub authentication on shell startup
- **Homebrew Support**: Automatic Homebrew environment setup on macOS
- **Custom Aliases**: Configurable shell aliases
- **History Management**: Optimized ZSH history settings
- **Shell Functions**: Useful shell functions like `reload-shell`
- **File Preservation**: Protects specified files from being overwritten

## Requirements

- ZSH shell installed on the target system
- Ansible 2.9 or higher
- Target user should have appropriate permissions for shell changes

## Role Variables

### User-Configurable Variables

The following variables can be set by users to customize the role behavior:

#### Shell Configuration

```yaml
# Whether to set ZSH as the default login shell for the user
zshconfig_set_default_shell: true
```

#### File Preservation

```yaml
# List of files in the ansible-managed directory to preserve
# These files will not be deleted even if they don't have corresponding templates
configure_zshconfig_preserve_files:
  - "20-oh-my-zsh.zsh"
  - "custom.zsh"
  - "local-overrides.zsh"
```

#### Aliases Configuration

```yaml
# Enable custom aliases configuration
configure_zshconfig_aliases: false

# Dictionary of custom aliases to create
zshconfig_aliases:
  ll: "ls -alF"
  gs: "git status"
  gp: "git pull"
  gc: "git commit"
  la: "ls -la"
```

#### GitHub Integration

```yaml
# Enable GitHub CLI login prompt on shell startup
zshconfig_github_login: true
```

### Internal Variables

These variables are used internally by the role and typically don't need to be modified:

```yaml
# Path to the main .zshrc file
zsh_rc_path: "{{ ansible_user_dir }}/.zshrc"

# Directory for zsh configuration fragments
zsh_rc_d_dir: "{{ ansible_user_dir }}/.zshrc.d"

# Directory for ansible-managed configuration files
zsh_rc_d_ansible_managed_dir: "{{ zsh_rc_d_dir }}/ansible-managed"

# Path to the zsh binary
zsh_path: /bin/zsh
```

## Configuration Files Created

The role creates the following configuration files:

### Core Files

- `~/.zshrc` - Main ZSH configuration file
- `~/.zshrc.d/ansible-managed/` - Directory for Ansible-managed configurations

### Ansible-Managed Configuration Fragments

- `0-aliases.zsh` - Custom shell aliases
- `0-functions.zsh` - Useful shell functions
- `0-github-login.zsh` - GitHub CLI authentication
- `0-homebrew.zsh` - Homebrew environment setup (macOS)
- `environment.zsh` - Environment variables and history settings

## Usage Examples

### Basic Usage

```yaml
- name: Configure ZSH for user
  include_role:
    name: zshconfig
```

### With Custom Aliases

```yaml
- name: Configure ZSH with custom aliases
  include_role:
    name: zshconfig
  vars:
    configure_zshconfig_aliases: true
    zshconfig_aliases:
      ll: "ls -alF"
      gs: "git status"
      gp: "git pull"
      gc: "git commit -m"
      gd: "git diff"
```

### Preserve Custom Files

```yaml
- name: Configure ZSH preserving custom configurations
  include_role:
    name: zshconfig
  vars:
    configure_zshconfig_preserve_files:
      - "20-oh-my-zsh.zsh"
      - "99-local-customizations.zsh"
      - "personal-functions.zsh"
```

### Disable GitHub Integration

```yaml
- name: Configure ZSH without GitHub integration
  include_role:
    name: zshconfig
  vars:
    zshconfig_github_login: false
```

### Complete Example

```yaml
- name: Full ZSH configuration setup
  include_role:
    name: zshconfig
  vars:
    zshconfig_set_default_shell: true
    zshconfig_github_login: true
    configure_zshconfig_aliases: true
    zshconfig_aliases:
      ll: "ls -alF"
      la: "ls -la"
      gs: "git status"
      gp: "git pull"
      gc: "git commit"
      gd: "git diff"
      reload: "source ~/.zshrc"
    configure_zshconfig_preserve_files:
      - "20-oh-my-zsh.zsh"
      - "personal-settings.zsh"
```

## Directory Structure

After running this role, the following directory structure will be created:

```text
~/.zshrc                              # Main ZSH configuration file
~/.zshrc.d/                          # Configuration fragments directory
├── ansible-managed/                 # Ansible-managed configurations
│   ├── 0-aliases.zsh               # Custom aliases
│   ├── 0-functions.zsh             # Shell functions
│   ├── 0-github-login.zsh          # GitHub CLI integration
│   ├── 0-homebrew.zsh              # Homebrew setup
│   └── environment.zsh             # Environment variables
└── *.zsh                           # User-defined configuration files
```

## Customization

### Adding Custom Configurations

Users can add their own ZSH configurations by:

1. **Creating files in `~/.zshrc.d/`**: Any `.zsh` files in this directory will be automatically sourced
2. **Using the preserve files feature**: Add files to `configure_zshconfig_preserve_files` to prevent them from being removed
3. **Extending templates**: The role's templates can be extended or overridden in playbooks

### Load Order

Configuration files are loaded in this order:

1. Ansible-managed files in `~/.zshrc.d/ansible-managed/` (alphabetically)
2. User files in `~/.zshrc.d/` (alphabetically)

This ensures that user configurations can override Ansible-managed defaults.

## Dependencies

None. This role is self-contained.

## Compatibility

- **Operating Systems**: Linux, macOS
- **Shell**: ZSH
- **Ansible**: 2.9+
