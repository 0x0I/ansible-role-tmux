Ansible Role :eyeglasses: tmux
=========
[![Galaxy Role](https://img.shields.io/ansible/role/44305.svg)](https://galaxy.ansible.com/0x0I/tmux)
[![Downloads](https://img.shields.io/ansible/role/d/44305.svg)](https://galaxy.ansible.com/0x0I/tmux)
[![Build Status](https://travis-ci.org/0x0I/ansible-role-tmux.svg?branch=master)](https://travis-ci.org/0x0I/ansible-role-tmux)

**Table of Contents**
  - [Supported Platforms](#supported-platforms)
  - [Requirements](#requirements)
  - [Role Variables](#role-variables)
      - [Install](#install)
      - [Config](#config)
  - [Dependencies](#dependencies)
  - [Example Playbook](#example-playbook)
  - [License](#license)
  - [Author Information](#author-information)

Ansible role that installs, configures and runs tmux: a terminal multiplexer enabling multiple terminals to be created, accessed, and controlled from a single screen.

##### Supported Platforms:
```
* Debian
* Redhat(CentOS/Fedora)
* Ubuntu
```

Requirements
------------

Requires pre-installation of the [libevent 2.x](http://fr2.rpmfind.net/linux/rpm2html/search.php?query=libevent&submit=Search+...&system=&arch=) and [ncurses](http://fr2.rpmfind.net/linux/rpm2html/search.php?query=ncurses&submit=Search+...&system=&arch=) software packages.

Role Variables
--------------
Variables are available and organized according to the following software & machine provisioning stages:
* _install_
* _config_

#### Install

`tmux`can be installed using OS package management systems provided by the supported platforms (e.g `apt`, `yum/dnf`).

_The following variables can be customized to control various aspects of this installation process:_

`service_package: <package-name-and-version>` (**default**: *tmux*[-latest])
- name and version of the tmux package to download and install. [Reference](http://fr2.rpmfind.net/linux/rpm2html/search.php?query=tmux&submit=Search+...&system=&arch=) or run something like `dnf --showduplicates list tmux` in a terminal to display a list of available packages for your platform

`tmux_user: <service-user-name>` (**default**: *tmux*)
- dedicated service user, group and directory used by `tmux` for privilege separation

#### Config

Using this role, configuration of an instance of `tmux` is managed with the `tmux_config` user variable and can be expressed within a hash, keyed by user account where appropriate. The value of these user account keys are either dicts, list of dicts or a combination thereof (set according to operator yaml syntax and customization preferences) representing associated startup/configuration commands/command-sequences (combinations of commands linked by execution) and an `optional` command-sequence comment.

The following provides an overview and example configurations for reference:

`[tmux_config:  {global | user-account} : {entry} :] comment: <string>` (**default**: see *none*)
- [optional] comment associated with configuration command or command-sequence

##### Example

 ```yaml
  tmux_config:
    global:
      - comment: "This comment provides context around the commands below (if any)"
        commands: []
  ```

`[tmux_config:  {global | user-account} : {entry} :] commands: <key: value,...>` (**default**: see `defaults/main.yml`)
- a collection of configuration commands to render within *user-account's* tmux.conf. A list of available command-line options can be found [here](http://man7.org/linux/man-pages/man1/tmux.1.html)

**Typically each key:value pair represents the `{command}: {flags}` components of a tmux command respectively:**

 ```yaml
  tmux_config:
    global:
      - comment: "Add ctrl-A as secondary prefix key"
        commands:
          # {command}: {flags} format
          - "set-option": "-g prefix2 C-a"
          - "bind-key": "C-a send-prefix -2"
  ```
  
However, technically any splitting of the tmux command syntax is allowed and should result in the correct rendering of the desired configuration:

 ```yaml
  tmux_config:
    user-account-1:
      - commands:
          - "bind-key C-c": "new-session"
          - "if-shell true": "{ display -p 'bar-dollar-foo: $foo' }"
          - "set-hook -g pane-mode-changed[42] 'set -g status-left-style bg=red'": ""
      - commands:
          - "set": "-g @plugin 'tmux-plugins/tpm'"
            "set -g": "@plugin 'tmux-plugins/tmux-sensible'"
        comment: "Install tmux TPM & 'tmux-sensible' (sensible configurations) plugins"
  ```
  
Dependencies
------------

None

Example Playbook
----------------
default example:
```
- hosts: all
  roles:
  - role: 0xOI.tmux
```

Apply Tmux "sensible (configurations)" plugin settings manually:
```
- hosts: all
  roles:
  - role: 0xOI.tmux
    vars:
      tmux_config:
        global:
          - comment: "utf8 is on"
            commands:
              - "set-option": "-g utf8 on"
              - "set-option": "-g status-utf8 on"
          - comment: "address vim mode switching delay (http://superuser.com/a/252717/65504)"
            commands:
              - "set-option": "-s escape-time 0"
          - comment: "increase scrollback buffer size"
            commands:
              - "set-option": "-g history-limit 50000"
          - comment: "tmux messages are displayed for 4 seconds"
            commands:
              - "set-option": "-g display-time 4000
          - comment: "refresh 'status-left' and 'status-right' more often"
            commands:
              - "set-option": "-g status-interval 5"
          - comment: "upgrade $TERM"
            commands:
              - "set-option": "-g default-terminal "screen-256color"
          - comment: "emacs key bindings in tmux command prompt (prefix + :) are bettter than vi keys, even for vim users"
            commands:
              - "set-option": "status-keys emacs"
          - comment: "focus events enabled for terminals that support them"
            commands:
              - "set-option": "-g focus-events on"
          - comment: "super useful when using "grouped sessions" and multi-monitor setup"
            commands:
              - "set-window-option": "-g aggressive-resize on"
          - comment: "easier and faster switching between next/prev window"
            commands:
              - "bind-key": "C-p previous-window"
              - "bind-key": "C-n next-window"
          - comment: "source .tmux.conf as suggested in `man tmux`"
            commands:
              - "bind-key": "R source-file '~/.tmux.conf'"
          - comment: "if prefix is 'C-a'"
            commands:
              - "bind-key": "C-a send-prefix"
              - "bind-key": "a last-window"
```
              
...or with a plugin installation:
```
- hosts: all
  roles:
  - role: 0xOI.tmux
    vars:
      tmux_config:
        global:
          - comment: "Add sensible to list of TPM plugins"
            commands:
              - "set-option": "-g @plugin 'tmux-plugins/tmux-sensible'"
```

License
-------

Apache, BSD, MIT

Author Information
------------------

This role was created in 2019 by O1 Labs.
