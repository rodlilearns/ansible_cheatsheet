# Ansible Cheat Sheet

## Automation Content Navigator
---

Run a playbook: 
`$ ansible-navigator run playbook.yml -i inventory -m stdout` 

Run a playbook in interactive mode: 
`$ ansible-navigator run playbook.yml -i inventory` 

Launch Automation Content Navigator: 
`$ ansible-navigator --eei <execution_env_image_name>` 
Run the playbook in interactive mode:
`:run <playbook.yml>` 
Display details for the play 
`<number>` 
Return to main playbook summary screen. 
`ESC` 
Exit the ansible-navigator command: 
`:q` 

Install Automation Content Navigator:
`$ sudo subscription-manager register`
`$ sudo subscription-manager repos --enable <ansible-automation-platform-_____-rpms>`
`$ sudo dnf install ansible-navigator`

*Ansible navigator runs the playbook inside a container that can't see ~/.ssh. However, ansible-navigator can provide those keys to the execution environment through the use of ssh-agent on the Control Node.* 

Start the ssh-agent on the Control Node: 
$ eval $(ssh-agent)

Provide passphrase for private key: 
$ ssh-add

### Subcommands 
| Automation content navigator subcommand | Description | CLI Example |
| builder | Build ee (container image) | ansible-navigator builder --help |
| collections | Explore available collections | ansible-navigator collections --help |
| config | Explore the current ansible configuration | ansible-navigator config --help |
| doc | Review documentation for a module or plugin | ansible-navigator doc --help |
| exec | Run a command within an ee | ansible-navigator exec --help |
| images | Explore ee images | ansible-navigator images --help |
| inventory | Explore an inventory | ansible-navigator inventory --help |
| lint | Lint a file or directory for common errors and issues | ansible-navigator lint --help |
| replay | Explore a previous run using a playbook artifact | ansible-navigator replay --help |
| run | Run a playbook | ansible-navigator run --help |
| settings | Review the current ansible-navigator settings | ansible-navigator settings --help |
| welcome | Start at the welcome page | ansible-navigator welcome --help |

*To run a subcommand in an interactive session, use : to start the command, and enter the subcommand. E.g. :config* 

## Installing Collections with a Requirements File 
--- 

```
---
collections:
  - name: <collection.name>

  - name: <collection.name>
    version: <version_number>
```

`$ ansible-galaxy collection install -r requirements.yml`


