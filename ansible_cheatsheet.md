# Ansible Cheat Sheet

## Automation Content Navigator
---

Install Automation Content Navigator:  
`$ sudo subscription-manager register`  
`$ sudo subscription-manager repos --enable <ansible-automation-platform-_____-rpms>`  
`$ sudo dnf install ansible-navigator`  

Run a playbook:  
`$ ansible-navigator run playbook.yml -i inventory -m stdout`  

Run a playbook in interactive mode:  
`$ ansible-navigator run playbook.yml -i inventory` 

Launch Automation Content Navigator:  
`$ ansible-navigator --eei <execution_env_image_name>`  
Run the playbook in interactive mode:  
`:run <playbook.yml>`  
Display details for the play:  
`<number>`  
Return to main playbook summary screen:  
`ESC`  
Exit the ansible-navigator command:  
`:q`  

Run a playbook using a specific automation execution environment:  
`$ ansible-navigator run <playbook.yml> --eei <registry_server_url>/<image>`  
*Make sure you are logged into the registry first with podman login*

Control the behaviour of the pulling of ee:  
`$ ansible-navigator run <playbook.yml> --pull-policy`

| Option | Description |
| ------ | ----------- |
| always | Always pulls the image |
| missing | Only pulls the image if not already available locally |
| never | Never pulls the image |
| tag | Pulls the image if the image's tag is `latest` or if not already available locally (the default) |

*Ansible navigator runs the playbook inside a container that can't see ~/.ssh. However, ansible-navigator can provide those keys to the execution environment through the use of ssh-agent on the Control Node.* 

Start the ssh-agent on the Control Node:  
`$ eval $(ssh-agent)`

Provide passphrase for private key:  
`$ ssh-add`

Inspect Ansible Configuration in Standard Output Mode:  
`$ ansible-navigator config -m stdout dump`

### Subcommands 
| Automation content navigator subcommand | Description | CLI Example |
| --------------------------------------- | ----------- | ----------- |
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

## Configuring Collection Sources

Add the following directives to the ansible.cfg file.

```
[galaxy]
server_list = <pah>, automation_hub, galaxy

[galaxy_server.<pah>]
url=<private_automation_hub_server_url/api/content/rh-certified>/
token=<token>

[galaxy_server.automation_hub]
url=<repo_server_url>/
auth_url=<url_to_access_distribution_platform>
token=<token>

[galaxy_server.galaxy]
url=https://galaxy.ansible.com/
```

## Creating Content Collections and EEs

1. Create a Collection Directory Structure:  
`$ ansible-galaxy collection init <namespace>.<collection>`  

2. Add Content to a Collection:  
For modules, create the `plugins/modules/` subdirectory and copy Python scripts for the modules into that subdirectory.  
For inventory plug-ins, create and use the `plugins/inventory/` subdirectory. 
For roles, move their directory structure under the `roles/` directory.  

3. Update Collection Metadata:  

4. Declare Collection Dependencies:  
Python dependencies require a requirements.txt file at the root of the collection.  
The version part at the end of the package name is optional if you don't require a specific version.  

```
botocore>=1.18.0
boto3>=1.15.0
boto>=2.49.0
```

Some collections require system-level packages in the EE.  
E.g. ansible.posix collection requires the rsync RPM package.  
At the root of the collection, create the bindep.txt file and list the RPM packages, one per line.  
`rsync [platform:centos-8 platform:rhel-8]`  
Another standard directive is [platform:rpm], which targets all Linux distributions that use the RPM packaging system.  

Add additional metadata for collection in the `meta/runtime.yml` file.  
If the collection requires a specific version of Ansible, then add the `requires_ansible` parameter to the file.  
```
---
requires_ansible: ">=2.10"
```  

Note: the meta/runtime.yml and the requires_ansible parameter are mandatory if you plan to publish your collection to Ansible Galaxy, Automation Hub, or Private Automation Hub. Otherwise, the validation process rejects the collection.  

4. Build the Collection:  
From inside the collection directory, run the build command:  
`$ ansible-galaxy collection build`  

5. Publish Collection:  
On PAH, navigate to Collections -> Namespaces  
Select <namespace>  
Click Upload collection  
Provide the .tar.gz file  
Approve Collection  

Alternatively  
Obtain an auth token  
Store it in `~/.ansible/galaxy_token`  
`$ ansible-galaxy collection publish <namespace>-<collection>.tar.gz