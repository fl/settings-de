Windows Services for Linux (WSL)
================================

Mit Hilfe der Windows-Erweiterung `Windows Services for Linux (WSL)` betreibt man auf Windows eine Linux Virtual Machine, die eng mit dem Gastbetriebssystem Windows verzahnt ist. Der Zugriff auf das Dateisystem des jeweiligen Gegenübers, also von Windows nach WSL/Linux oder umgekehrt, ist ohne Konfiguration möglich.

Ausserdem kann man viele Entwicklerwerkzeuge so konfigurieren, dass sie WSL verwenden und damit Linux-Werkzeuge direkt auf Linux nutzen, ohne die EInschränkungen mit denen man leben lernt wenn man Windows als Entwicklermaschine benutzt und Linux als Zielplattform.

Lokalen Proxy in einer WSL instanz aktivieren
---------------------------------------------

install the ``cntlm`` package

create password hashes for your credentials::

    isabell@stardust:~$ cntlm -d uberspace.de -u isabell -H
    Password:
    PassLM          C3BBEAC7BC189747552C4BCA4AEBFB11
    PassNT          E7166513CD4F28EA24C8DB3067329AA4
    PassNTLMv2      B37A2A1B173CEF3602E94DCB0917F2B7    # Only for user 'isabell', domain 'uberspace.de'

configure hashes, port, exclusions in the config file ``/etc/cntlm.conf``

Linux in WSL zur Verwendung des likalen Proxys einrichten
---------------------------------------------------------

To use the local ``cntlm`` proxy installed in the previous step, use the settings for the proxy host and port as displayed in the examples here. To use a different proxy, adapt hostname and port. You can look up the currently configured proxy settings in the Windows system settings item "Proxy settings".

In der Shell
^^^^^^^^^^^^

create the file ``/etc/profile.d/proxy.sh`` with the following content::

    #!/bin/sh
    http_proxy="http://localhost:3128"; export http_proxy
    https_proxy="$http_proxy"; export https_proxy

APT Konfiguration
^^^^^^^^^^^^^^^^^

create the file ``/etc/apt/apt.conf.d/80proxy`` with the following content::

    # proxy
    Acquire::http::Proxy "http://localhost:3128";
    Acquire::https::Proxy "http://localhost:3128";

Setup für einen MITM proxy
--------------------------

Some environments mandate that users submit to having their traffic inspected at the proxy, including TLS traffic. To achieve that, those sites usually configure their proxy to act as a CA to the internal client, and as a normal SSL client to servers offering the requested service on the public internet. For this configuration to work without user interaction (with the exception of server using HSTS), administrators deploy the CA certificate of the proxy to the trust chain of managed clients. On  a Windows machine, it will be visible in the Certificates MMC snap-in (the tool can be started as ``C:\Windows\System32\certmgr.msc``) in the folder "Trusted Root Certificates".

For Python tools, such as `pip` or `pip-compile`, configure the CA cert to be trusted needs to be configured with the option ``--cert``::

    pip --cert C:\certs\cacert.pem install pip-tools

For Java applications, import the CA certificate in the JRE or JDK certificate store.









