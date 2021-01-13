Nginx Webserver
===============

Ansible role to configure the `nginx` webserver and `acmetool` LE client.


Variables
---------

* `nginx__dhparam_size` (Default 2048):
  The DH parameters bit length.

* `nginx__global_snippet_files`:
  List of global snippets, independent of specific vhost configuration.

* `nginx__vhost_snippet_files`:
  List of snippets common to all vhost, dependent of specific vhost configuration.

* `nginx_sites` (Default `{}`):
  The virtual hosts configurations for this webserver.


Files
-----

Note: Path segments `host_files` when resolved by the `hf` lookup plugin always
      include full `hf` functionality, for example also search the `group_files`
      directory as apropriate.


* Main `nginx` configuration file template
  Lookup path:
    - `<playbook_dir>/files/host_files/<inventory_hostname>/nginx/nginx.conf`  [via `hf` lookup]
    - `<playbook_dir>/files/nginx/nginx.conf`  [via `hf` lookup]
    - `<role_dir>/files/nginx/nginx.conf`  [default role fallback]


* Global (default and vhost independent) configuration snippets
  Lookup path:
    - `<playbook_dir>/files/host_files/<inventory_hostname>/nginx/snippets/<snippetname>.snippet.conf`  [via `hf` lookup]
    - `<playbook_dir>/files/nginx/snippets/<snippetname>.snippet.conf`  [via `hf` lookup]
    - `<role_dir>/files/nginx/snippets/<snippetname>.snippet.conf`  [default role fallback]
  Note: All `<snippetname>`s must be listed in the `nginx__global_snippet_files` variable.
  TODO: Unlisted inclusion via file glob (needs combination of host_files and globbing)?


* Main configuration file for each virtual host (usually contains corresponding nginx `server` block)
  Lookup path (tls):
    - `<playbook_dir>/files/nginx/sites/<vhost_name>_tls.conf`
  Lookup path (http):
    - `<playbook_dir>/files/nginx/sites/<vhost_name>_http.conf`  [via `first_found` lookup]
    - `<role_dir>/files/nginx/sites-available/vhost_http_redirect.conf.j2`  [default role fallback]


* Per virtual host custom snippets
  Lookup path:
    - `<playbook_dir>/files/nginx/snippets/*_<vhost_name>.snippet.conf`  [via `fileglob` lookup]
  Note: In general, content of such snippets could be merged with main vhost configuration file.



* Per virtual host basic auth file
  Lookup path:
    - unimplemented

* Per virtual host robots file
  Lookup path:
    - unimplemented


Example
-------

Configuration of the virtual hosts in the `host_vars` of the webserver:

```
nginx_sites:
  - name: 'example.org'
    altnames:                              Optional, for acmetool
      - 'www.example.org'
      - 'ftp.example.org'
    robots: 'robots_allow_all.txt'         Optional, unimplemented
    htaccess: 'htpasswd.example.org'       Optional, unimplemented
    webroot:                               Optional, for use with 'webhost' role
       path                                Optional, for use with 'webhost' role
       user                                Optional, for use with 'webhost' role
       group                               Optional, for use with 'webhost' role
       mode                                Optional, for use with 'webhost' role
```

Alternatively, put this data into a suitable `group_vars` file.


References
----------

* [Nginx documentation](https://nginx.org/en/docs/)

* [acmetool](https://github.com/hlandau/acmetool)
* [acmetool user's guide](https://hlandau.github.io/acmetool/userguide)
