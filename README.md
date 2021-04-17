# Ansible Role: Vim

[![CI](https://github.com/skaary/ansible-role-vim/actions/workflows/ci.yml/badge.svg?branch=main&event=push)](https://github.com/skaary/ansible-role-vim/actions?query=workflow%3Ci)

An Ansible Role that installs [Vim](https://www.vim.org/) and plugins on Linux.

This role uses either Vim 8's native package manager (see `:help packages`) or [Pathogen](https://github.com/tpope/vim-pathogen) depending on the Vim version found by this role.

For Vim 8 the plugins will be installed by default to the following locations:

- `~/.vim/pack/plugins/start`
- `~/.vim/pack/plugins/opt`

For Pathogen the plugins will be installed by default to the following location:

- `~/.vim/bundle/`

It is important to note that Vim's native package management does **not** automatically create helptags. This role can be used as the package manager (i.e. run it every time a plugin is added or removed) to manage helptag creation.

If your use case does not involve using the role for _all_ Vim plugin management, you will need to manage helptags using a method like one of the following:

- run `:helptags ALL` from inside Vim,
- script it (on cron or another trigger like [Git hooks](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html))
  something like this role does it: `vim -c 'helptags ~/.vim/pack' -c q`

## Requirements

None

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable name | Default value | Description |
|---------------|---------------|-------------|
| `vim_user`                   | `""`              | The system user to install Vim and/or associated plugins for. |
| `vim_group`                   | `""`              | The group of the user specificed in `vim_user`. |
| `vim_installed_packages`      | `["vim"]`         | A list of packages to install (passed to Ansible's Package module). |
| `vim_removed_packages`        | `[]`              | A list of packages to remove (e.g. on Ubuntu, it might be preferable to remove `vim-tiny`). |
| `vim_pack_subdirectory`       | `plugins` | The name of the directory to place plugins installed by this role into the default creates e.g. `~/.vim/pack/plugins/start` and `~/.vim/pack/plugins/opt`. |
| `vim_installed_plugins_start` | `[]`              | The list of plugins to install to `~/.vim/pack/plugins/start`--see `:help packages` for details on what this means. Should contain a `repo` property and (optionally) a `version` property as used by Ansible's Git module. |
| `vim_installed_plugins_opt`   | `[]`              | The list of plugins to install to `~/.vim/pack/plugins/opt`--see `:help packages` for details on what this means. Should contain a `repo` property and (optionally) a `version` property as used by Ansible's Git module. |
| `vim_removed_plugins_start`   | `[]`              | A list of directory names to remove from `~/.vim/pack/plugins/start`. |
| `vim_removed_plugins_opt`     | `[]`              | A list of directory names to remove from `~/.vim/pack/plugins/opt`. |

If pathogen is used, the variables used for adding / removing plugins are `vim_installed_plugins_start` and `vim_removed_plugins_start`, respectively.

## Example Playbook

 ---
    - hosts: all
      vars:
        vim_user: "molecule"
        vim_group: "molecule"
        vim_removed_packages:
          - "vim-tiny"
        vim__installed_plugins_start:
          - repo: "https://github.com/scrooloose/nerdcommenter.git"
        vim__installed_plugins_opt:
          - repo: "https://github.com/godlygeek/tabular.git"
        vim_removed_plugins_start:
          - "nerdcommenter"
        vim_removed_plugins_opt:
          - "tabular"
      roles:
        - role: ansible-role-vim

## License
