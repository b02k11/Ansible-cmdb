Ansible Configuration Management Database (CMDB)
=========================================


About
-----

Ansible-cmdb takes the output of Ansible's fact gathering and converts it into
a static HTML overview page containing system configuration information. It also support some other output like : Json, CSV, Markdown and SQL.

It supports multiple types of output (html, csv, sql, etc) and extending
information gathered by Ansible with custom data. For each host it also shows
the groups, host variables, custom variables and machine-local facts.

![overview](image.png)

![localhost](image-1.png)


Features
--------

(Not all features are supported by all templates)

* Automated Data Collection – Collects infrastructure data using Ansible Playbooks.
* Dynamic Reporting – Generates dynamic reports in formats like HTML and JSON.
* Customizable Output – Customizable report formats and details.
* Extensibility and Plugins – Supports custom modules and integrations.
* Multi-format Export – Exports data in formats like JSON, CSV, and XML.
* Web Interface for Viewing Data – Provides a web UI for visualizing data.
* Open Source – Free and community-supported, with regular updates.
* Host overview and detailed host information.
* Host and group variables.
* Gathered host facts and manual custom facts.
* Adding and extending facts of existing hosts and manually adding entirely
  new hosts.
* Custom columns


Getting started
---------------


1. Install Ansible-cmdb from [source, a release
   package](https://github.com/fboender/ansible-cmdb/releases) or through pip: `pip
   install ansible-cmdb`.

1. Fetch your host's (localhost) facts through ansible:

        $ mkdir out
        $ ansible -m setup --tree out/ localhost
    For generating the information using inventory.

  
   ```
    $ ansible -m setup -i inventory.ini --tree out/ all
   ```
  

1. Generate the CMDB HTML with Ansible-cmdb:

        $ ansible-cmdb out/ > overview.html

1. Open `overview.html` in your browser.

That's it! Please do read the full documentation on usage, as there are some
caveats to how you can use the generated HTML.

## About

Ansible cmdb takes the output of
Ansible's [fact
gathering](http://docs.ansible.com/ansible/latest/modules/setup_module.html)
and converts it into a static HTML overview page (and other things) containing
system configuration information.

It supports multiple types of output (html, csv, sql, etc) and extending
information gathered by Ansible with custom data. For each host it also shows
the groups, host variables, custom variables and machine-local facts.

## Example output

![alt text](image-2.png)


## Output formats

Supported output formats / templates:

* Fancy HTML (`--template html_fancy`), as seen in the screenshots above.
* Fancy HTML Split (`--template html_fancy_split`), with each host's details
  in a separate file (for large number of hosts).
* CSV (`--template csv`), the trustworthy and flexible comma-separated format.
* JSON (`--template json`), a dump of all facts in JSON format.
* Markdown (`--template markdown`), useful for copy-pasting into Wiki's and
  such.
* Markdown Split ('--template markdown_split'), with each host's details
  in a seperate file (for large number of hosts).
* SQL (`--template sql`), for importing host facts into a (My)SQL database.
* Plain Text table (`--template txt_table`), for the console gurus.
* and of course, any custom template you're willing to make.



## Requirements

Ansible-cmdb requires **Python v2.7+ / 3.0+**.

In theory, it should work on any system that can run Python, including BSD,
Linux, Windows, Solaris and MacOS. In practice, ansible-cmdb is developed on
Ubuntu 16.04 and tested on the latest stable versions of Debian, Ubuntu and
Centos.

## Installation


Ansible-cmdb can be installed using `pip`, the [Python package
manager](https://pypi.org/project/pip/). There are also stand-alone packages
for various Linux distributions. Alternatively, you can use brew or plain old
`make install`.

### Through Pip

For **installation via Pip**:

Install `pip` [for your distribution](https://packaging.python.org/install_requirements_linux/)
if you don't have it yet.

Install Ansible-cmdb through Pip:

    sudo pip install ansible-cmdb

You can also upgrade Ansible-cmdb through Pip:

    sudo pip install --upgrade ansible-cmdb

### Through distribution packages

Get the package for your distribution from the [Releases
page](https://github.com/fboender/ansible-cmdb/releases) (Not required for
MacOS X install)

For **Debian / Ubuntu** systems:

    sudo dpkg -i ansible-cmdb*.deb

Support for all other package managers (RPM, etc) has been dropped. Please use
the `pip` method instead, or install from tar.gz.

## Getting started

This chapter assumes you've already [installed](installation.md) Ansible-cmdb.

First, generate Ansible output for your hosts:

    mkdir out
    ansible -m setup --tree out/ all

Next, call ansible-cmdb on the resulting `out/` directory to generate the CMDB
overview page:

    ansible-cmdb out/ > overview.html

!!! Note
    `ansible-cmdb` is a shell wrapper script that tries its best to find the
    correct Python binary to use and the proper location of the
    `ansible-cmdb.py` script. If it fails to do so, you can manually specify
    the path to the correct python interpreter and script. E.g.:

    /opt/python3/bin/python /opt/ansible-cmdb/src/ansible-cmdb.py

By default, the `html_fancy` template is used, which generates output
containing an overview of all your hosts, with a section of detailed
information for each host.

You can now open `overview.html` in your browser to view the results.

## Full usage

    Usage: ansible-cmdb [option] <dir> > output.html

    Options:
      --version             show program's version number and exit
      -h, --help            show this help message and exit
      -t TEMPLATE, --template=TEMPLATE
                            Template to use. Default is 'html_fancy'
      -i INVENTORY, --inventory=INVENTORY
                            Inventory to read extra info from
      -f, --fact-cache      <dir> contains fact-cache files
      -p PARAMS, --params=PARAMS
                            Params to send to template
      -d, --debug           Show debug output
      -q, --quiet           Don't report warnings
      -c COLUMNS, --columns=COLUMNS
                            Show only given columns
      -C CUST_COLS, --cust-cols=CUST_COLS
                            Path to a custom columns definition file
      -l LIMIT, --limit=LIMIT
                            Limit hosts to pattern
      --exclude-cols=EXCLUDE_COLUMNS
                            Exclude cols from output

## Host and group variables

Ansible-cmdb reads and includes the host and group variables from the
inventory. When you point ansible-cmdb to your host inventory (`hosts` file,
usually) with the `-i` option, ansible-cmdb automatically includes information
from the `host_vars` and `group_vars` directories if found in the same dir.

Whether the variables are included in the output depends on the template used.
In the `html_fancy` templates, variables will become available under the
"Custom variables" heading.

The ''html_fancy'' template supports four special variables:

- `groups`: A list of Ansible groups the host belongs to.
- `dtap`: Whether a host is a development, test, acceptance or production
   system.
- `comment`: A comment for the host.
- `ext_id`: An external unique identifier for the host.


## Specifying templates

ansible-cmdb offers multiple templates. You can choose your template with the
`-t` or `--template` argument:

    ansible-cmdb -t tpl_custom out/ > overview.html

The 'html_fancy' template is the default.  

Templates can be referred to by name or by relative/absolute path to the
`.tpl` file. This lets you implement your own templates. For example:

    $ ansible-cmdb -t /home/fboender/my_template out/ > my_template.html

## Available templates

Ansible-cmdb currently provides the following templates out of the box:

* **`html_fancy`**: A dynamic, modern HTML page containing all hosts.
* **`html_fancy_split`**: A dynamic, modern HTML page with each host's details in a separate file.
* **`txt_table`**: A quick text table summary of the available hosts with some minimal information.
* **`json`**: Dumps all hosts including groups, variable, custom info in JSON format.
* **`csv`**: The CSV template outputs a CSV file of your hosts.
* **`markdown`**: The Markdown template generates host information in the Markdown format.
* **`sql`**: The SQL template generates an .sql file that can be loaded into an SQLite or MySQL database.

**html_fancy**:

`html_fancy` is currently the default template.

A fancy HTML page that uses jQuery and DataTables to give you a searchable,
sortable table overview of all hosts with detailed information just a click
away.

**html_fancy_split**:

This template is basically the same as the **html_fancy** template, but it
generates a `cmdb/` directory with an `index.html` file and a separate html
file for each host's details. This is useful if you have a large amount of
hosts and the html_fancy template is rendering too slow.

Usage:

    ansible-cmdb -t html_fancy_split -i hosts out/ 

It accepts the same parameters as the `html_fancy` template.

**sql**:

The `sql` template generates an .sql file that can be loaded into an SQLite or
MySQL database.

    $ ansible-cmdb -t sql -i hosts out > cmdb.sql
    $ echo "CREATE DATABASE ansiblecmdb" | mysql
    $ mysql ansiblecmdb < cmdb.sql

![alt text](image-4.png)

## Custom facts
To include the custom fact we can create Json file in /etc/ansible/fact.d 
that will be included under ansible locat in fact file while collecting facts.

```
"ansible_local": {
            "app3_25": {
                "ModemManager.service": {
                    "name": "ModemManager.service",
                    "source": "systemd",
                    "state": "running",
                    "status": "enabled"
                },
                "NetworkManager-dispatcher.service": {
                    "name": "NetworkManager-dispatcher.service",
                    "source": "systemd",
                    "state": "inactive",
                    "status": "enabled"
                },
                "NetworkManager-wait-online.service": {
                    "name": "NetworkManager-wait-online.service",
                    "source": "systemd",
                    "state": "stopped",
                    "status": "enabled"
                }
            }
}

```

## Custom columns

You can add custom columns to the host overview with the `-C` (`--cust-cols`)
option.

Custom columns are currently only supported by the `html_fancy` and
`html_fancy_split` templates.

The `-C` option takes a parameter which is the path to a file containing
your custom column definitions. The file's syntax is Python (even though it
looks like JSON). An example can be found in the
`examples/cust_cols.conf` file in the repo:

    [
        # Show whether AppArmor is enabled
        {
            "title": "AppArmor",
            "id": "apparmor",
            "sType": "string",
            "visible": False,
            "jsonxs": "ansible_facts.ansible_apparmor.status"
        },
        # Show the nameservers configured on the host
        {
            "title": "Nameservers",
            "id": "nameservers",
            "sType": "string",
            "visible": True,
            "tpl": """
              <ul>
                <%
                # Get ansible_facts.ansible_dns.nameservers
                facts = host.get('ansible_facts', {})
                dns = facts.get('ansible_dns', {})
                nameservers = dns.get('nameservers', [])
                %>
                % for nameserver in nameservers:
                  <li>${nameserver}</li>
                % endfor
              </ul>
            """
        },
        # Show the nameservers configured on the host, but use jsonxs.
        {
            "title": "Nameservers2",
            "id": "nameservers2",
            "sType": "string",
            "visible": True,
            "tpl": """
              <ul>
                <%
                # Get ansible_facts.ansible_dns.nameservers using jsonxs
                nameservers = jsonxs(host, 'ansible_facts.ansible_dns.nameservers', default=[])
                %>
                % for nameserver in nameservers:
                  <li>${nameserver}</li>
                % endfor
              </ul>
            """
        }
    ]

This defines two new columns: 'AppArmor' and 'Nameservers'. Each column
consist of the following key/values:

* `title` is what is displayed as the columns user-friendly title. **Required**.
* The `id` key must have a unique value, to differentiate between columns.
  **Required**
* The `sType` value determines how the values will be sorted in the host
  overview. Possible values include `string` and `num`. **Required**
* `visible` determines whether the column will be active (shown) by default.

To use it:

    ansible-cmdb -C example/cust_cols.conf -i example/hosts example/out/ > cmdb.html

When opening the `cmdb.html` file in your browser, you may have to hit the
'Clear settings' button in the top-right before the new columns show up or
when you get strange behaviour.

## Custom template in SQL
To create custom coloum in sql template we can customize /ansible-cmdb/src/ansiblecmdb/data/tpl/sql.tpl file and can include information from ansible fact.
```
<%def name="col_load_avg(host)"><%
  return jsonxs(host, 'ansible_facts.ansible_loadavg.5m', default='')
%></%def>
```
Ansible-cmdb Repository : [Ansible-cmdb-github](https://github.com/fboender/ansible-cmdb)

Ansible-cmdb official document : [Ansible-cmdb](https://ansible-cmdb.readthedocs.io/en/latest/)
