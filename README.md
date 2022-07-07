# Ansible Role: Vim

[![CI](https://github.com/skaary/ansible-role-vim/actions/workflows/ci.yml/badge.svg?branch=main&event=push)](https://github.com/skaary/ansible-role-vim/actions?query=workflow%3Ci)

An Ansible Role that installs [Vim](https://www.vim.org/) and vim plugins on Linux.

## Installation

Download the role directly from git by typing into your terminal:

```bash
$ ansible-galaxy install git+https://github.com/skaary/ansible-role-vim.git
```
or

```bash
$ ansible-galaxy install git+https://github.com/skaary/ansible-role-vim.git,,vim
```

to change the installed role name from _ansible-role-vim_ to just _vim_.

Alternatively, install the role via a _requirements.yml_ file, e.g. when installing multiple roles at once. See [ansible galaxy documentation](https://galaxy.ansible.com/docs/using/installing.html#installing-multiple-roles-from-a-file) for more information.

## Plugin manager

This role uses either Vim 8's native package manager (see `:help packages`) or [Pathogen](https://github.com/tpope/vim-pathogen) depending on the Vim version determined by this role.

For **Vim 8's native package manager** the plugins will be installed by default to the following paths:

- `~/.vim/pack/plugins/start`
- `~/.vim/pack/plugins/opt`

It is important to note that Vim's native package management does **not** automatically create helptags. This role can be used as the package manager (i.e. run it every time a plugin is added or removed) to manage helptag creation.

If your use case does not involve using the role for _all_ Vim plugin management, you will need to manage helptags using a method like one of the following:

- run `:helptags ALL` from inside Vim,
- script it (on cron or another trigger like [Git hooks](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html))
  something like this role does it: `vim -c 'helptags ~/.vim/pack' -c q`

For **Pathogen** the plugins will be installed by default to the following path:

- `~/.vim/bundle/`

Also, to activate pathogen the following line needs to be added to your `.vimrc`:

```bash
execute pathogen#infect()
syntax on
filetype plugin indent on
```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable name                 | Default value | Description                                                                                                                                                                                                                 |
| ----------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `vim_user`                    | `""`          | The system user to install Vim and/or associated plugins for.                                                                                                                                                               |
| `vim_group`                   | `""`          | The group of the user specificed in `vim_user`.                                                                                                                                                                             |
| `vim_installed_packages`      | `["vim"]`     | A list of packages to install (passed to Ansible's Package module).                                                                                                                                                         |
| `vim_removed_packages`        | `[]`          | A list of packages to remove (e.g. on Ubuntu, it might be preferable to remove `vim-tiny`).                                                                                                                                 |
| `vim_pack_subdirectory`       | `plugins`     | The name of the directory to place plugins installed by this role into. E.g., the default uses `~/.vim/pack/`**`plugins`**`/start` and `~/.vim/pack/`**`plugins`**`/opt`.                                                                  |
| `vim_installed_plugins_start` | `[]`          | The list of plugins to install to `~/.vim/pack/plugins/start`--see `:help packages` for details on what this means. Should contain a `repo` property and (optionally) a `version` property as used by Ansible's Git module. |
| `vim_installed_plugins_opt`   | `[]`          | The list of plugins to install to `~/.vim/pack/plugins/opt`--see `:help packages` for details on what this means. Should contain a `repo` property and (optionally) a `version` property as used by Ansible's Git module.   |
| `vim_removed_plugins_start`   | `[]`          | A list of directory names to remove from `~/.vim/pack/plugins/start`.                                                                                                                                                       |
| `vim_removed_plugins_opt`     | `[]`          | A list of directory names to remove from `~/.vim/pack/plugins/opt`.                                                                                                                                                         |

If pathogen is used, the variables used for adding / removing plugins are `vim_installed_plugins_start` and `vim_removed_plugins_start`, respectively.

## Example Playbook

```yaml
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
```

## Testing the role

### Vagrant

Vagrant can be used to test the role in order to graphically see it working in a virtual machine. Make sure Vagrant and VirtualBox are installed:

```bash
$ sudo apt install vagrant virtualbox
```

Use the following commands to run vagrant and boot up the virtual machine:

```bash
$ cd tests
$ vagrant up
```

Use `vagrant destroy` after you are done testing to delete the virtual machine. For more information about Vagrant and its commands, see the [Vagrant documentation](https://www.vagrantup.com/docs/cli).

### Molecule with Docker

Molecule can be used to test the role with a docker container. Make sure Molecule is installed:

```bash
$ python3 -m pip install --user "molecule[docker]"
```

Use the following commands to run Molecule in order to create the docker container and access the created container:
```bash
$ molecule converge && molecule login
```

For more information on how to use Molecule please consult the [Molecule documentation](https://molecule.readthedocs.io/en/latest/getting-started.html).

> Note: Python and Docker are required for the use of molecule. For more information, see [Molecule installation](https://molecule.readthedocs.io/en/latest/installation.html).

## License

MIT / BSD

