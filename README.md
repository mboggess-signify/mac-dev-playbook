# Mac Signify SRE Ansible Playbook

This playbook builds off of a couple of external Ansible roles:
- [mac-dev-playbook Ansible role](https://github.com/geerlingguy/mac-dev-playbook)
- [dotfiles](https://github.com/geerlingguy/dotfiles)
- [ansible-osx-command-line-tools](https://github.com/elliotweiser/ansible-osx-command-line-tools)

There are some customizations that have been made for Signify SRE. Over all, it installs and configures a significant portion of the software needed to get up and running as an SRE at Signify.

As Jeff says in the intro to his playbook, some things in macOS are slightly difficult to automate, so there are still a few manual installation steps, but those should all still be documented here

## Installation

  1. Ensure the Signify Privileges application is installed, and before starting
     this instalation process, toggle the privileges on.
  1. Ensure Apple's command line tools are installed (`xcode-select --install` to launch the installer).
  1. [Install Homebrew](https://brew.sh/#install):

     1. Install Homebrew: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
     1. Add Homebrew directory to your $PATH: `export PATH=export PATH=/usr/local/bin:/usr/local/sbin:$PATH`
        Note: a more permanent setting of your $PATH will be handled in later steps

  1. [Install Python and Pip](https://docs.python-guide.org/starting/install3/osx/)

     1. Install Python: `brew install python`
     1. Ensure you're running this brew-installed version, not the version of Python
        that comes with your system: `which python3`
  
  1. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html):

     1. Run the following command to add Python 3 to your $PATH: `export PATH="$HOME/Library/Python/3.9/bin:/opt/homebrew/bin:$PATH"`
     1. Upgrade Pip: `sudo pip3 install --upgrade pip`
     1. Install Ansible: `pip3 install ansible`

  1. Clone or download this repository to your local drive.
  1. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
  1. Run `ansible-playbook main.yml --ask-become-pass` inside this directory. Enter your macOS account password when prompted for the 'BECOME' password.

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `k9s`, `extra-packages` and `osx`.

    ansible-playbook main.yml -K --tags "dotfiles,homebrew"

## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - cowsay
  - git
  - go

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: '^8.1'

gem_packages:
  - name: bundler
    state: latest

npm_packages:
  - name: webpack

pip_packages:
  - name: mkdocs
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Included Applications / Configuration (Default)

Applications (installed with Homebrew Cask):

  - None, at this time, because basic ones (Chrome, Slack) are installed on your first boot of your device. Docker, tho required, must be installed via Self- Service due to licensing.

Packages (installed with Homebrew): see default.config.yml for up-to-date list.


Dotfiles are  installed into the current user's home directory, including the `.osx` dotfile for configuring many aspects of macOS for better performance and ease of use. *Make sure to read through the .osx script to ensure you understand what changes will be made by it.* You can disable dotfiles management by setting `configure_dotfiles: no` in your configuration.

## Ansible for DevOps

Check out [Ansible for DevOps](https://www.ansiblefordevops.com/), which teaches you how to automate almost anything with Ansible.

## Attribution

This project was originally created by [Jeff Geerling](https://www.jeffgeerling.com/) (originally inspired by [MWGriffin/ansible-playbooks](https://github.com/MWGriffin/ansible-playbooks)).
