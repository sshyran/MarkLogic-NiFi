---
layout: inner
title: Getting Started
lead_text: ''
permalink: /getting-started
---

# Getting Started

## Install MarkLogic

Follow the [Installation Guide][install-guide] found on the MarkLogic Documentation site. If you are familiar with the MarkLogic install process you can find the installation files [here][ml-installers] or you can use an existing MarkLogic installation.

## Install NiFi

For detailed instructions on the installation process see [Apache NiFi: Getting Started][nifi-getting-started]. 

Download the latest binary package from [NiFi's Download page][nifi-downloads] and uncompress the file to the desired location.

## Install MarkLogic NiFi Processors 

Add the MarkLogic NiFi nar files to the `<NIFI_HOME>\lib` directory. Binaries can be found in the [nifi-nars GitHub repository][nifi-nars-binaries].

## Start NiFi

On Windows, run the `bin/run-nifi.bat` file. On Mac/\*nix systems, run from the terminal `sh bin/nifi.sh start`.

[install-guide]:http://docs.marklogic.com/guide/installation/procedures#id_28962
[ml-installers]:https://developer.marklogic.com/products
[nifi-getting-started]:https://nifi.apache.org/docs/nifi-docs/html/getting-started.html#downloading-and-installing-nifi
[nifi-downloads]:http://nifi.apache.org/download.html
[nifi-nars-binaries]:https://github.com/marklogic/nifi/releases
