# Git Configuration Role

An Ansible role that manages Git configuration for a user, providing a modular and extensible approach to Git setup with support for signing, aliases, and custom configurations.

## Description

This role configures Git by:

- Creating a modular Git configuration structure using `.gitconfig.d/` directory
- Managing Git configuration files through Ansible templates
- Setting up user identity (name and email)
- Configuring Git signing with GPG or SSH keys
- Managing Git aliases for improved productivity
- Supporting custom Git configurations
- Maintaining separation between Ansible-managed and user-defined settings

The role creates a main `.gitconfig` file that includes configuration fragments from both Ansible-managed and user-defined directories, allowing for clean separation between automated and manual configurations.

## Features

- **Modular Configuration**: Uses `.gitconfig.d/` directory structure for organized configuration management
- **Ansible-Managed Files**: Separates automated configurations from user customizations
- **User Identity Setup**: Configurable Git user name and email
- **Commit Signing**: Support for GPG and SSH key signing
- **Git Aliases**: Customizable Git command aliases
- **Default Branch**: Configurable default branch name (e.g., main)
- **Auto Push Setup**: Automatic remote branch setup on push
- **File Management**: Automatically manages configuration files and cleanup

## Requirements

- Git installed on the target system
- Ansible 2.9 or higher
- Target user should have appropriate permissions for file operations
- GPG or SSH keys configured if signing is desired

## Role Variables

### User-Configurable Variables

The following variables can be set by users to customize the role behavior:

#### User Identity

```yaml
# Git user name (defaults to ansible_user_id)
gitconfig_user_name: "John Doe"

# Git user email (defaults to ansible_user_id@ansible_hostname.local)
gitconfig_user_email: "john.doe@example.com"
```

#### Commit Signing

```yaml
# Path to signing key (optional)
# If defined and not empty, commit signing will be enabled
gitconfig_signingkey: "/Users/username/.ssh/id_rsa"

# Signing key format: "gpg" or "ssh"
gitconfig_signingkey_format: "ssh"
```

#### Git Behavior

```yaml
# Default branch name for new repositories
gitconfig_init_defaultBranch: "main"

# Automatically setup remote tracking on push
gitconfig_push_autoSetupRemote: true
```

#### Git Aliases

```yaml
# Dictionary of Git aliases
gitconfig_aliases:
  co: "checkout"
  br: "branch"
  ci: "commit"
  st: "status"
  unstage: "reset HEAD --"
  last: "log -1 HEAD"
  visual: "!gitk"
```

## Configuration Files Created

The role creates the following files and directories:

### Core Files

- `~/.gitconfig` - Main Git configuration file with include statements
- `~/.gitconfig.d/` - Directory for Git configuration fragments
- `~/.gitconfig.d/ansible-managed/` - Directory for Ansible-managed configurations

### Ansible-Managed Configuration Fragments

- `0.gitconfig` - Core Git configuration including user, signing, aliases, and behavior settings

## Usage Examples

### Basic Usage

```yaml
- name: Configure Git with basic settings
  include_role:
    name: gitconfig
  vars:
    gitconfig_user_name: "John Doe"
    gitconfig_user_email: "john.doe@example.com"
```

### With SSH Signing

```yaml
- name: Configure Git with SSH commit signing
  include_role:
    name: gitconfig
  vars:
    gitconfig_user_name: "Jane Smith"
    gitconfig_user_email: "jane.smith@company.com"
    gitconfig_signingkey: "~/.ssh/id_ed25519"
    gitconfig_signingkey_format: "ssh"
```

### With GPG Signing

```yaml
- name: Configure Git with GPG commit signing
  include_role:
    name: gitconfig
  vars:
    gitconfig_user_name: "Developer"
    gitconfig_user_email: "dev@example.org"
    gitconfig_signingkey: "ABCD1234EFGH5678"
    gitconfig_signingkey_format: "gpg"
```

### With Custom Aliases

```yaml
- name: Configure Git with productivity aliases
  include_role:
    name: gitconfig
  vars:
    gitconfig_user_name: "Power User"
    gitconfig_user_email: "poweruser@dev.local"
    gitconfig_aliases:
      co: "checkout"
      br: "branch"
      ci: "commit"
      st: "status"
      df: "diff"
      lg: "log --oneline --graph --decorate --all"
      unstage: "reset HEAD --"
      last: "log -1 HEAD"
      amend: "commit --amend"
      pushf: "push --force-with-lease"
```

### Complete Setup

```yaml
- name: Full Git configuration setup
  include_role:
    name: gitconfig
  vars:
    gitconfig_user_name: "Full Stack Developer"
    gitconfig_user_email: "fullstack@company.com"
    gitconfig_signingkey: "~/.ssh/id_ed25519"
    gitconfig_signingkey_format: "ssh"
    gitconfig_init_defaultBranch: "main"
    gitconfig_push_autoSetupRemote: true
    gitconfig_aliases:
      # Shortcuts
      co: "checkout"
      br: "branch"
      ci: "commit"
      st: "status"
      df: "diff"

      # Useful commands
      unstage: "reset HEAD --"
      last: "log -1 HEAD"
      amend: "commit --amend"
      pushf: "push --force-with-lease"

      # Logging
      lg: "log --oneline --graph --decorate --all"
      hist: "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"

      # Branching
      new: "checkout -b"
      delete-branch: "branch -D"
```

### Without Signing

```yaml
- name: Configure Git without commit signing
  include_role:
    name: gitconfig
  vars:
    gitconfig_user_name: "Simple User"
    gitconfig_user_email: "simple@example.com"
    gitconfig_signingkey: "" # Explicitly disable signing
```

## Directory Structure

After running this role, the following directory structure will be created:

```text
~/.gitconfig                          # Main Git configuration file
~/.gitconfig.d/                      # Configuration fragments directory
├── ansible-managed/                 # Ansible-managed configurations
│   └── 0.gitconfig                 # Core Git configuration
└── *.gitconfig                     # User-defined configuration files
```

## Git Configuration Sections

The role configures the following Git sections:

### User Section

```ini
[user]
    name = Your Name
    email = your.email@example.com
    signingkey = /path/to/key  # (if configured)
```

### Signing Settings

```ini
[gpg]
    format = ssh  # or gpg

[commit]
    gpgsign = True  # (if signing key is configured)
```

### Default Behavior

```ini
[init]
    defaultBranch = main

[push]
    autoSetupRemote = True
```

### Aliases

```ini
[alias]
    co = checkout
    br = branch
    ci = commit
    # ... other configured aliases
```

## Customization

### Adding Custom Configurations

Users can extend Git configuration by:

1. **Creating files in `~/.gitconfig.d/`**: Any `.gitconfig` files in this directory will be automatically included
2. **Using descriptive naming**: Files are loaded in alphabetical order, so use prefixes like `10-custom.gitconfig`
3. **Avoiding conflicts**: User files can override Ansible-managed settings

### Load Order

Configuration files are loaded in this order:

1. Ansible-managed files in `~/.gitconfig.d/ansible-managed/` (alphabetically)
2. User files in `~/.gitconfig.d/` (alphabetically, excluding ansible-managed directory)

This ensures that user configurations can override Ansible-managed defaults.

### Common Git Aliases

Here are some popular Git aliases you might want to include:

```yaml
gitconfig_aliases:
  # Basic shortcuts
  co: "checkout"
  br: "branch"
  ci: "commit"
  st: "status"
  df: "diff"

  # Staging operations
  unstage: "reset HEAD --"
  staged: "diff --cached"

  # Commit operations
  amend: "commit --amend"
  last: "log -1 HEAD"

  # Logging and history
  lg: "log --oneline --graph --decorate --all"
  hist: "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
  timeline: "log --graph --branches --pretty=oneline --decorate"

  # Branch management
  new: "checkout -b"
  delete-branch: "branch -D"
  rename-branch: "branch -m"

  # Remote operations
  pushf: "push --force-with-lease"
  sync: "!git fetch origin && git rebase origin/main"

  # Utilities
  ignored: "ls-files --others --ignored --exclude-standard"
  untracked: "ls-files --others --exclude-standard"
```

## Security Considerations

### Signing Configuration

- **SSH Keys**: Modern and simpler approach, works with GitHub/GitLab
- **GPG Keys**: Traditional approach, requires GPG setup
- **Key Security**: Ensure signing keys are properly secured and backed up

### File Permissions

- Configuration files are created with appropriate permissions (644)
- Directories are created with secure permissions (755)

## Integration with Development Workflow

This role works well with other development setup roles:

1. **SSH Setup**: Configure SSH keys before enabling SSH signing
2. **GPG Setup**: Install and configure GPG if using GPG signing
3. **Shell Configuration**: Works alongside `zshconfig` for complete development environment

## Troubleshooting

### Common Issues

1. **Signing Errors**: Verify signing key exists and is properly configured
2. **Permission Errors**: Ensure user has write access to home directory
3. **Alias Conflicts**: Check for conflicting aliases in existing Git configuration
4. **Include Path Issues**: Verify `.gitconfig.d` directory structure is correct

### Validation

To verify the configuration is working:

```bash
# Check Git configuration
git config --list

# Test user configuration
git config user.name
git config user.email

# Test aliases
git st  # Should work if 'st' alias is configured

# Test signing (if configured)
git log --show-signature -1
```

## Dependencies

None. This role is self-contained.

## Compatibility

- **Operating Systems**: Linux, macOS, Windows (WSL)
- **Git Versions**: Git 2.0+
- **Ansible**: 2.9+
