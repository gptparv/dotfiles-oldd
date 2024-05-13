# Dotfiles <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->

- [Skating on thin ice?](#skating-on-thin-ice)
- [Getting Started](#getting-started)
  - [defaults](#defaults)
  - [clean](#clean)
  - [link](#link)
  - [shell](#shell)
  - [brewfile](#brewfile)
  - [template](#template)
  - [git](#git)
  - [asdf](#asdf)
- [References](#references)


## Skating on thin ice?

> [!CAUTION]
> This section is only for the author of the repository.
> Look at the rest of the Readme to understand what it is configuring and modify for your needs.
>
> Once the modifications have been made, you can run the listed commands.

The setup is idempotent, i.e. you can run it multiple times without worrying about different results.

1. Install the Xcode Command Line Tools. Run the command, and follow the prompts.

```shell
xcode-select --install
```

2. Pull the repository from Github:

```shell
git clone https://github.com/gptparv/dotfiles ~/.dotfiles
```

3. Navigate to the `.dotfiles` directory:

```shell
cd ~/.dotfiles
```

4. Make the script executable:

```shell
chmod +x install
```

5. Run the installation and grab a coffee:

```shell
./install
```

> [!TIP]
> The script might fail at certain steps, just run it again.

## Getting Started

dotfiles are the configuration files in your home directory which configures your system. Common examples are: *zshrc*, *bashrc* etc. 

A tool named [dotbot](https://github.com/anishathalye/dotbot) is used to bootstrap the dotfiles. dotbot can do more than just creating symbolic links to your dotfiles in the home directory. It uses something called as directives which provides various funcationalities. With directives you can create symbolic links, directories, run shell commands/scripts. 

dotbot also has a plugin ecosystem which allows custom directives. The plugins are installed as git submodules. Four plugins are installed to configure more than what the default tools provides.

The plugins are:

- *dotbot-asdf*: Install asdf plugins
- *dotbot-brewfile*: Install brew dependencies, bundle style
- *dotbot-git*: Clone and pull git repositories
- *dotbot-template*: Template files using Jinja

dotbot takes a configuration file [install.conf.yaml](install.conf.yaml) which can be customized to your needs. The execute the tool, a bash script [install](install) is available.

### defaults

The default section in the provides default properties to the directives.

```yaml
- defaults:
    link:
      relink: true
    brewfile:
      stdout: true
      stderr: true
      include: ['tap', 'brew', 'cask', 'mas', 'vscode']
    shell:
      stdout: true
      stderr: true
```

### clean

Cleans the directories that should be checked for dead symbolic links. These dead links are removed automatically. We provide users home directory to be cleaned.

```yaml
- clean: ['~']
```

### link

This is the core of dotbot. Creating the symbolic links for the dotfiles. zsh is my default shell, so I have a custom [zshrc](configs/zshrc) file. For the command line prompt, I use [Starship](https://starship.rs) which takes a configuration file named [starship.toml](configs/starship.toml). 

```yaml
- link:
    ~/.zshrc: configs/zshrc
    ~/.config/starship.toml: 
      create: true
      path: configs/starship.toml
```

### shell

Next up is the shell directive, which allows execution of shell commands and scripts. Two things are done here: 

1. Command to install/update the submodules.
2. Execute a script [brew](src/brew) to install Homebrew, if not present. Otherwise, updates homebrew.

```yaml
- shell:
  - [git submodule update --init --recursive, Install/Update submodules]
  - [chmod +x src/brew; ./src/brew, "Install Homebrew, update if already present"]
```

### brewfile

[Homebrew](https://brew.sh/) is the package manager for MacOS. It can install CLI tools and GUI applications. Everything can be bundled in a file called [Brewfile](Brewfile).

```yaml
- brewfile:
    file: Brewfile
```

### template

template directive is used to configure Jinja templates. All the templates are available in [templates](templates) directory.

> [!WARNING]
> Change the params in the template directive as per your needs.

```yaml
- template:
  - ~/.gitconfig:
      source_file: templates/gitconfig
      params:
        NAME: Parv Gupta
        EMAIL: 85178397+gptparv@users.noreply.github.com
        GITHUB_USERNAME: gptparv
```

### git

[ohmyzsh](https://github.com/ohmyzsh/ohmyzsh) is a framework to customize the zsh shell. It needs to be installed, along with some other repositories for zsh add-ons. 

```yaml
- git:
    '~/.oh-my-zsh':
        url: 'https://github.com/ohmyzsh/ohmyzsh.git'
        description: 'oh-my-zsh'
    '~/.oh-my-zsh/custom/plugins/zsh-autosuggestions':
        url: 'https://github.com/zsh-users/zsh-autosuggestions'
        description: 'zsh-autosuggestions'
    '~/.oh-my-zsh/custom/plugins/zsh-completions':
        url: 'https://github.com/zsh-users/zsh-completions'
        description: 'zsh-completions'
    '~/.oh-my-zsh/custom/plugins/eza':
        url: 'https://github.com/eza-community/eza.git'
        description: 'eza completions'
```

### asdf

[asdf-vm](https://asdf-vm.com/) is a manager to install multiple versions of a tool. asdf is enabled through ohmyzsh plugin in the [zshrc](configs/zshrc). Here we are only adding the plugins and not installing them.

```yaml
- asdf:
  - plugin: terraform
  - plugin: packer
  - plugin: terraform-docs
```

## References

- [dotbot](https://github.com/anishathalye/dotbot)
- [dotbot-asdf](https://github.com/sobolevn/dotbot-asdf)
- [dotbot-brewfile](https://github.com/sobolevn/dotbot-brewfile)
- [dotbot-git](https://github.com/DrDynamic/dotbot-git)
- [dotbot-template](https://github.com/ssbanerje/dotbot-template)
