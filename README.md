OpenStack Images
================

This role generates guest instance images using disk-image-builder
and uploads them to OpenStack using the `os_image` module.

Requirements
------------

The OpenStack APIs should be accessible from the target host.
Client credentials should have been set in the environment, or
using the `clouds.yaml` format.

Role Variables
--------------

`os_images_cache`: a path to a directory in which to cache build artefacts.
It defaults to `~/disk_images`

`os_images_auth_type`: OpenStack authentication endpoint and credentials.
Defaults to `password`.

`os_images_auth`: OpenStack authentication endpoint and credentials.  For example, a dict of the form:
* `auth_url`: Keystone auth endpoint URL.  Defaults to `OS_AUTH_URL`.
* `project`: OpenStack tenant/project.  Defaults to `OS_TENANT_NAME`.
* `username`: OpenStack username.  Defaults to `OS_USERNAME`.
* `password`: OpenStack password.  Defaults to `OS_PASSWORD`.

`os_images_list` is a list of YAML dicts, each containing:
* `name`: the image name to use in OpenStack.
* `elements`: a list of diskimage-builder elements to incorporate into the image.
* `env`: (optional) environment variables to define for diskimage-builder parameters.
  This is a dict of the form of `KEY: VALUE`.
* `size`: (optional) size to make the image filesystem.
* `properties`: (optional) dict of properties to set on the glance image.

`os_images_common`: A set of elements to include in every image listed.
Defaults to `cloud-init enable-serial-console stable-interface-names`.

`os_images_dib_version`: Optionally set a version of diskimage-builder to install.
By default this is not constrained.

`os_images_git_elements`: An optional list of elements to pull from github, deploy
locally for incorporation into the images.  Supply a list of dicts with the
following parameters:
* `repo`: URL to a git repo for cloning (if not already present)
* `local`: local path for git cloning
* `ref`: optional git reference (branch, tag, hash) for cloning.  Defaults to `HEAD`

`os_images_elements`: An optional list of paths for site-specific DIB elements.

`os_images_upload`: Whether to upload built images to Glance. Defaults to `True`.

Dependencies
------------

Example Playbook
----------------

The following playbook generates a guest image and uploads it to OpenStack:

    ---
    - name: Generate guest image and upload
      hosts: openstack
      roles:
        - role: stackhpc.os-image
          os_images_auth:
            auth_url:     "{{ lookup('env','OS_AUTH_URL') }}"
            username:     "{{ lookup('env','OS_USERNAME') }}"
            password:     "{{ lookup('env','OS_PASSWORD') }}"
            project_name: "{{ lookup('env','OS_TENANT_NAME') }}"
          os_images_list:
          - name: FedoraCore
            elements:
              - fedora
              - selinux-permissive
              - alaska-extras
            env:
              DIB_ALASKA_DELETE_REPO: "y"
              DIB_ALASKA_PKGLIST: "pam-python pam-keystone"

Author Information
------------------

- Stig Telfer (<stig@stackhpc.com>)
