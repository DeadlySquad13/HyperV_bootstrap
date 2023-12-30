# HyperV Bootstrap

Create HyperV machines from *existing* vhdx disks.

> Creating from 0 is also possible by alternating code a little bit but
> you may be better off using Vagrant. This repo was created just to fill in
> a gap of a simple and fast migration, not full-fledged HyperV configuration.

## Get started
Use `./bin/dot-bootstrap` (should be executable) to install required packages for
ansible. Once installed press cancel ('3') because you're most likely will need
some extra variables set in the vault.

To configure variables for current host (most importantly password) go to `group_vars`
and create a new host with variables. Passwords here are only referencing encrypted vault.
To edit vault you have to find a master password inn keepass and populate `.vault_pass.py` in the root of the repo.
See `.vault_pass.py.example` for example. You will most likely need to make `.vault_pass.py` executable.

After that you will be ready to edit vault:
```sh
ansible-vault edit --vault-password-file .vault_pass.py ./group_vars/<host_name>/vault
```

After that you're ready to go!

## Common pitfalls
### Hosts
Note that when you're running playbook, it will try to deploy it to all the hosts in `hosts.ini` file.
Limit it to the only hosts you need.

### Ansible.cfg in world writable directory
When running from shared directory in multisystem setup (Windows + WSL included) you will get errors and `ansible.cfg`
will be ignored (and hosts too). To fix it, the easiest way is to pass it directly as a variable when running scipt:
```sh
ANSIBLE_CONFIG=ansible.cfg ./bin/dot-bootstrap
```

## Structure
Add configurations for VMs into `vars` directory.
Add configurations for HyperV hosts you're going to run. Here also specify
`vm_name` that matches one of configured VMs.
> Only one VM per machine is supported (it's easy to fix by adding another loop
> in playbook).

Inside `roles/hyperv_vm/tasks` main tasks for bootstrapping are located.

> [!WARNING]
> Generation 1 VMs don't have a lot of configuration options available. You may
> need to modify playbook a lot for it to run successfully.


Inside `library` folder you can find scripts for managing HyperV via Ansible.
Most notable is `library/win_hyperv_guest.ps1` where guest is configured.

