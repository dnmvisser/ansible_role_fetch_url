ansible_role_fetch_url
=========

Fetch a URL and save the content to a file, but only if:

- The file does not yet exist
- The reported `content-length` header value is different from the size of the
  existing file

This can save resources, for example when fetching large binaries that are regularly updated

Role Variables
--------------

* `url` The URL to fetch content from
* `path` The path to save the content to
* `mode` Mode
* `owner` Owner
* `group` Group


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
- hosts: servers
  become: true
  tasks:
    - name: Fetch latest docker-machine binary
      ansible.builtin.import_role:
        name: ansible_role_fetch_url
      vars:
        url: https://gitlab-docker-machine-downloads.s3.amazonaws.com/v0.16.2-gitlab.40/docker-machine-Linux-x86_64
        path: /usr/local/bin/docker-machine
        mode: "0755"
```
