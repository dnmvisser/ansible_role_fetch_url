# `ansible_role_fetch_url`

Fetch a URL and save the content to a file, but only if:

- The file does not yet exist
- The file exists, but the [`ETag`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag)
  header of the resource is different from the stored version

This can save time and resources, for example when fetching large binary files.

To avoid having to maintain a separate table of file names to `ETag` values, we
save the `Etag` in an extended attribute on the downloaded file.

# Requirements

This role uses the
[`community.general.xattr`](https://docs.ansible.com/ansible/latest/collections/community/general/xattr_module.html)
module, so it has [those
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
| `fetch_url_xattr_etag`| `user.etag` | Extended attribute to store the `ETag` |
| `fetch_url_xattr_url` | `user.xdg.origin.url` | For informational purpose, store the URL as an extended attribute. Per [recommendation](https://www.freedesktop.org/wiki/CommonExtendedAttributes/)) |


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

The `ETag` value contains double quotes, but the [`community.general.xattr`](https://docs.ansible.com/ansible/latest/collections/community/general/xattr_module.html) module does not handle that correctly. To avoid problems, we store the base64 encoded value instead.
