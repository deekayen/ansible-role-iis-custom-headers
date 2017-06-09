IIS Custom Headers
==================

Add or remove global, custom HTTP headers from IIS web sites on Microsoft Windows Server.

Role Variables
--------------

The `iis_custom_headers` default variable defines which key and value to set for custom HTTP headers. If you wish to override it, you'll need to re-define all the headers that should be `present` or `absent`. Ansible will not merge your overridden variable with the defaults. The `X-Frame-Options` header is defined by the IETF in [RFC7034](https://tools.ietf.org/html/rfc7034) to improve web application security.

By default, Ansible will restart IIS at the end of the tasks because it usually assumes the `win_shell` execution results in a change; toggle `iis_custom_headers_restart_iis` to prevent the IIS restart.

    iis_custom_headers:
      - { name: "X-Frame-Options", value: "SAMEORIGIN", state: "present" }
      - { name: "X-Powered-By", value: "ASP", state: "absent" }
      - { name: "X-Powered-By", value: "ASP.NET", state: "absent" }

    iis_custom_headers_restart_iis: True

Example Playbook
----------------

The tasks rely on using Powershell's web server (IIS) administration cmdlets [Add-WebConfigurationProperty](https://technet.microsoft.com/en-us/library/ee790572.aspx) and [Remove-WebConfigurationProperty](https://technet.microsoft.com/en-us/library/ee790570.aspx).

    - hosts: iis_servers
      roles:
         - role: deekayen.iis_custom_headers
           iis_custom_headers:
             - { name: "Strict-Transport-Security", value: "max-age=31536000", status: "present" }
             - { name: "X-Frame-Options", value: "SAMEORIGIN", status: "present" }
             - { name: "X-Powered-By", value: "ASP.NET", status: "absent" }

The result will add [customHeaders](https://www.iis.net/configreference/system.webserver/httpprotocol/customheaders) elements to the global XML configuration:

    <configuration>
       <system.webServer>
          <httpProtocol>
             <customHeaders>
                <add name="Strict-Transport-Security" value="max-age=31536000" />
                <add name="X-Frame-Options" value="SAMEORIGIN" />
             </customHeaders>
          </httpProtocol>
       </system.webServer>
    </configuration>


Requirements
------------

* IIS

Dependencies
------------

None.

License
-------

BSD
