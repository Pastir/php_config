Role Name
=========
ansible-php_config

Role Variables
--------------
### Default value of preset variables:

```
php_version: "8.1"
php_conf_dir: "/etc/php/{{ __php_version }}"
php_conf_cli_dir: "{{ __php_conf_dir }}/cli"
php_conf_fmp_dir: "{{ __php_conf_dir }}/fpm"
php_conf_fpm_pool_dir: "{{ __php_conf_fpm_dir }}/pool.d"
php_conf_fpm_file: "{{ __php_conf_fpm_dir }}/php-fpm.conf"

```

### php.ini
- See all directives descriptions https://www.php.net/manual/en/ini.core.php and https://www.php.net/manual/en/configuration.changes.php
- Default php.ini
``` 
__php_ini: 
  - short_open_tag: Off
  - precision: 14
  - output_buffering: 4096
  - zlib.output_compression: Off
  - implicit_flush: Off
  - unserialize_callback_func:
  - serialize_precision: -1
  - zend.enable_gc: On
  - zend.exception_ignore_args: On
  - zend.exception_string_param_max_len: 0
  - expose_php: On
  - max_execution_time: 30
  - max_input_time: 60
  - memory_limit: -1
  - error_reporting: E_ALL & ~E_DEPRECATED & ~E_STRICT
  - display_errors: Off
  - display_startup_errors: Off
  - log_errors: On
  - ignore_repeated_errors: Off
  - ignore_repeated_source: Off
  - report_memleaks: On
  - variables_order: "GPCS"
  - request_order: "GP"
  - register_argc_argv: Off
  - auto_globals_jit: On
  - post_max_size: 8M
  - default_mimetype: "text/html"
  - default_charset: "UTF-8"
  - enable_dl: Off
  - file_uploads: On
  - upload_max_filesize: 2M
  - max_file_uploads: 20
  - allow_url_fopen: On
  - allow_url_include: Off
  - default_socket_timeout: 60
  - cli_server.color: On
  - SMTP: localhost
  - smtp_port: 25
  - mail.add_x_header: Off
  - odbc.allow_persistent: On
  - odbc.check_persistent: On
  - odbc.max_persistent: -1
  - odbc.max_links: -1
  - odbc.defaultlrl: 4096
  - odbc.defaultbinmode: 1
  - pgsql.log_notice: 0
  - bcmath.scale: 0
  - session.save_handler: files
  - session.use_strict_mode: 0
  - session.use_cookies: 1
  - session.use_only_cookies: 1
  - session.name: PHPSESSID
  - session.auto_start: 0
  - session.cookie_lifetime: 0
  - session.cookie_path: /
  - session.serialize_handler: php
  - session.gc_probability: 0
  - session.gc_divisor: 1000
  - session.gc_maxlifetime: 1440
  - session.cache_limiter: nocache
  - session.cache_expire: 180
  - session.use_trans_sid: 0
  - session.sid_length: 26
  - session.trans_sid_tags: "a=href,area=href,frame=src,form="
  - session.sid_bits_per_character: 5
  - zend.assertions: -1
  - tidy.clean_output: Off
  - soap.wsdl_cache_limit: 5
  - ldap.max_links: -1

```

Example Playbook
----------------

    - hosts: servers
      vars:
        - cli_php_ini:
          - memory_limit: 256M
          - max_execution_time: 300
          - max_input_time: 300

        - fpm_php_ini:
          - memory_limit: 256M
          - max_execution_time: 600
          - max_input_time: 600

      roles:
         - { role: pastir.php_config }


### php-fpm.conf
- See all global directives descriptions  https://www.php.net/manual/en/install.fpm.configuration.php
- Default php-fpm.conf
```
__fpm_conf:
  - pid: "/run/php/php{{ php_version }}-fpm.pid"
  - error_log: "/var/log/php{{ php_version }}-fpm.log"
  - include: "/etc/php/{{ php_version }}/fpm/pool.d/*.conf"
```
Example Playbook
----------------

    - hosts: servers
      vars:
        - cli_php_ini:
          - memory_limit: 256M
          - max_execution_time: 300
          - max_input_time: 300

        - fpm_php_ini:
          - memory_limit: 256M
          - max_execution_time: 600
          - max_input_time: 600

        - fpm_conf:
          - pid: "/run/php/php{{ php_version }}-fpm.pid"
          - error_log: "/var/log/php{{ php_version }}-fpm.log"
          - include: "/etc/php/{{ php_version }}/fpm/pool.d/*.conf"
          - log_level: notice
          - log_buffering: no


      roles:
         - { role: pastir.php_config }


### pool.conf
- See all list of pool directives descriptions  https://www.php.net/manual/en/install.fpm.configuration.php
- Default name pool www -> www.conf
- support sections 

  - listen_params
  - pm
  - ping
  - logs
  - process
  - security
  - access
  - php_admin_value
  - php_admin_flag
  - php_value
  - php_flag
```
  __fpm_pools:
    - name: www
      user: www-data
      group: www-data
      listen: /run/php/$pool.sock
      listen_params:
        owner: www-data
        group: www-data
        mode: "0660"
        allowed_clients: 127.0.0.1
      pm:
        mode: dynamic
        max_children: 5
        start_servers: 2
        min_spare_servers: 1
      ping:
        path: /ping
        response: pong
      logs:
        access_log: /var/log/php/www.access.log
        slow_log: /var/log/php/www.slow.log
        request_slowlog_timeout: 3
      php_admin_value:
        error_log: /var/log/php/www.error.log
        memory_limit: 2048M
        max_execution_time: 300
      php_admin_flag:
        log_errors: true
      php_value:
        upload_max_filesize: 20M
        post_max_size: 20M
```
Example Playbook
----------------

    - hosts: servers
      vars:
        - cli_php_ini:
          - memory_limit: 256M
          - max_execution_time: 300
          - max_input_time: 300

        - fpm_php_ini:
          - memory_limit: 256M
          - max_execution_time: 600
          - max_input_time: 600

        - fpm_conf:
          - pid: "/run/php/php{{ php_version }}-fpm.pid"
          - error_log: "/var/log/php{{ php_version }}-fpm.log"
          - include: "/etc/php/{{ php_version }}/fpm/pool.d/*.conf"
          - log_level: notice
          - log_buffering: no

        - fpm_pools:
          - name: "www"
            pm:
              mode: static
              max_children: 16
              max_requests: 1024
              status_path: /status
            ping:
              path: /ping
              response: pong
            logs:
              access.log: /var/log/php/$pool.access.log
              slowlog: /var/log/php/$pool.slow.log
              request_slowlog_timeout: 3
            php_admin_value:
              error_log: /var/log/php/$pool.error.log
              memory_limit: 2048M
              max_execution_time: 300
            php_admin_flag:
              log_errors: True
            php_value:
              upload_max_filesize: 20M
              post_max_size: 20M

      roles:
         - { role: pastir.php_config }
