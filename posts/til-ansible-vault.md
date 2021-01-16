---
title: "TIL: use Ansible Vault to manage secrets"
description: Ansible Vault makes it easy to store and use secrets when managing infrastructure
date: 2021-01-02 
tags:
   - ansible
   - til

layout: layouts/post.njk
---
To fully automate the process of deploying and registering Gitlab Runner instances for my KVM Conjurer project, I needed to use a Gitlab personal access token. Since this key could allow an attacker to arbitrarily execute code it's very important to secure it.

Ansible provides a solution with [the `ansible-vault` command](https://docs.ansible.com/ansible/latest/user_guide/vault.html). You can use this tool to encrypt files in a way that still provides easy access for Ansible scripts. Encrypting a file goes like this:

``` shell
$ ansible-vault encrypt --vault-id gitlab/.ansible_vault_passwd secrets/gitlab.yml
```

This encrypts the file `secrets/gitlab.yml` using the password labelled `gitlab` in the `.ansible_vault_passwd` file. From here, you can use the encrypted variables stored in `secrets/gitlab.yml` when running a playbook like this:

``` shell
$ ansible-playbook --vault-id gitlab/.ansible_vault_passwd \
	 	   -e secrets/gitlab.yml \
		   some-playbook.yml
```

If you need to edit the `secrets/gitlab.yml` file, Ansible will decrypt it for editing and open it in its default editor with this command:

``` shell
$ ansible-vault edit --vault-id gitlab/.ansible_vault_passwd secrets/gitlab.yml
```

Finally, if you're using these encrypted secrets in a particular task in a playbook, use the `no_log` directive:

``` yml
- name: use some secret stuff to do something that requires auth
  no_log: True
  access_some_api:
	. . . 
```

This ensures there won't be a cleartext record of the secrets left behind when your configuration is complete. The Ansible documentation [I linked earlier](https://docs.ansible.com/ansible/latest/user_guide/vault.html) explains more features and further best practices for using Ansible Vault and for protecting secrets while using Ansible in general.
