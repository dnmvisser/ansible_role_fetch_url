# `ansible_role_fetch_url`

Fetch a URL and save the content to a file, but only if:

- The file does not yet exist
- The [`E-Tag` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag)
  on the resouces has changed

This is accomplished by saving the `E-tag` header value in an extended attribute
on the downloaded file. This can save resources, for example when fetching large binaries etc.

# Requirements

This role uses the
[`community.general.xattr`](https://docs.ansible.com/ansible/latest/collections/community/general/xattr_module.html) module, so it has [those
requirements](https://docs.ansible.com/ansible/latest/collections/community/general/xattr_module.html#synopsis)
as well.


# Role Variables

|  variable  | default |  description |
|-------------|--------|--------------|
| `url` |  | The URL to fetch content from |
| `path` |  | The path to save the content to |
| `mode` |  | File mode |
| `owner` | | File owner |
| `group` |  | File group |
| `fetch_url_xattr_etag_hash`| `user.etag_hash` | Extended attribute to store the hash of the `E-Tag` header value in |
| `fetch_url_xattr_url` | `user.xdg.origin.url` | For informational purpose, store the URL as an extended attribute. Per [recommendation](https://www.freedesktop.org/wiki/CommonExtendedAttributes/)) |
| `fetch_url_hash_algo` | `sha256` | Hashing algorithm to use for hashing the `E-Tag` header value |


# Example Playbook


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

# BUGS

The value of the `E-Tag` header contains double quotes. But the [`community.general.xattr`](https://docs.ansible.com/ansible/latest/collections/community/general/xattr_module.html) module seems to have some problem with handling those. Therefore we store a hashed version instead.
