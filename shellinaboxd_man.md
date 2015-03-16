# shellinaboxd #
## SYNOPSIS ##
<pre>
*shellinaboxd*  [ *-b* |    *--background*[=_pidfile_] ]    [ *-c* |    *--cert*=_certdir_ ]<br>
[ *--cert-fd*=_fd_ ] [ *--css*=_filename_ ] [ -*-cgi*[=_portrange_] ] [ *-d* |<br>
*--debug* ]  [ *-f* |  *--static-file*=_url_:_file_ ] [ *-g* | *--group*=_gid_ ]<br>
[ *-h* |      *--help* ]      [ *--linkify*=[*none*|*normal*|*aggressive*] ]<br>
[ *--localhost-only* ]  [ *--no-beep* ]  [ *-n* | *--numeric* ] [ *--pid-*<br>
*file*=_pidfile_ ] [ *-p* | *--port*=_port_ ]  [ *-s* |  *--service*=_service_ ]<br>
[ *-t* |  *--disable-ssl* ]  [ *--disable-ssl-menu* ] [ *-q* | *--quiet* ]<br>
[ *-u* |  *--user*=_uid_ ]  [ *--user-css*=_styles_ ]  [ *-v* |  *--verbose* ]<br>
[ *--version* ]<br>
</pre>

## DESCRIPTION ##
The **shellinaboxd** daemon implements a webserver that listens on the specified
_port_. The web server publishes one or more _services_ that will be displayed
in a VT100 emulator implemented as an AJAX web application. By default, the port
is 4200 and the default service URL is http://localhost:4200/.

If no particular _service_ was requested, the server launches **/bin/login**
querying the user for their username and password. It then starts the
user's default login shell.

Any modern JavaScript and CSS enabled browser will be able to access the
published _service_ without requiring additional plugins.

## OPTIONS ##
The following command line parameters control the operation of the daemon:
  * **-b** | **--background**`[`=_pidfile_]
> > Launch **shellinaboxd** as a background daemon process. Optionally, write the
> > process id to _pidfile_.
  * **-c** | **--cert**=_certdir_
> > If built with SSL/TLS support enabled, the daemon will look in _certdir_
> > for any certificates. If unspecified, this defaults to the current
> > working directory.
> > <p />
> > If the browser negotiated a **Server Name Identification** the daemon will
> > look for a matching _certificate-_<b>SERVERNAME</b>.pem_file. This allows
> > for virtual hosting of multiple server names on the same IP address and port.
> >_<p />
> > If no **SNI** handshake took place, it falls back on using the certificate in the
> > _certificate.pem_ file.
> > <p />
> > The administrator should make sure that there are matching certificates for
> > each of the virtual hosts on this server, and that there is a generic
> > _certificate.pem_ file.
> > <p />
> > If no suitable certificate is installed, **shellinaboxd** will attempt to
> > invoke **/usr/bin/openssl** and create a new self-signed certificate. This only
> > succeeds if, after dropping privileges, **shellinaboxd** has write permissions for
> > _certdir_.
> > <p />
> > Most browsers show a warning message when encountering a self-signed
> > certificate and then allow the user the option of accepting the
> > certificate. Due to this usability problem, and due to the perceived
> > security implications, the use of auto-generated self-signed
> > certificates is intended for testing or in intranet deployments, only.
  * **--cert-fd**=_fd_
> > Instead of providing a **--cert** directory, it is also possible to provide a
> > filedescriptor _fd_ where the certificate and key can be retrieved. While this
> > option disables **SNI** support, it does offer an alternative solution for
> > securely providing the private key data to the daemon.
  * **--css**=_filename_
> > Sometimes, it is not necessary to replace the entire style sheet using
> > the **--static-file** option. But instead a small incremental change should be
> > made to the visual appearance of the terminal. The **--css** option provides a
> > means to append additional style rules to the end of the default **styles.css**
> > sheet. More than one **--css** option can be given on the same command line.
  * **--cgi**`[`=_portrange_]
> > Instead of running **shellinaboxd** as a permanent process, it can be demand-loaded
> > as a CGI web server extension. When doing so, it will spawn a server that lives for
> > the duration of the user's session. If an optional _portrange_ of the
> > form **MINPORT**-**MAXPORT** has been provided, the server limits itself to these port
> > numbers. They should be configured to pass through the firewall.
> > <p />
> > The **--cgi** option is mutually exclusive with the **--background**, **--pidfile**
> > and **--port** options.
> > <p />
> > In order to be useful as a CGI script, the **shellinaboxd** binary probably will have
> > to be made **setuid-root**. This is currently a discouraged configuration. Use with
> > care.
  * **-d** | **--debug**
> > Enables debugging mode, resulting in lots of log messages on _stderr_. This
> > option is mutually exclusive with **--quiet** and **--verbose**.
  * **-f** | **--static-file=**_url_:_file_
> > The daemon serves various built-in resources from URLs underneath the _service_
> > mount points. One or more **--static-file** options allow for overriding these
> > resources with customized externally provided _files_. The _url_ can either be
> > an absolute or a relative path. In the former case, it overrides exactly one
> > built-in resource for one specific _service_, whereas in the latter case it
> > overrides resources for each defined _service_.
> > <p />
> > The following resources are available for customization:
    * **beep.wav** audio sample that gets played whenever the terminal BEL is sounded.
    * **favicon.ico** favicon image file that is displayed in the browser's navigation bar.
    * **ShellInABox.js** JavaScript file implementing the AJAX terminal emulator.
    * **styles.css** CSS style file that controls the visual appearance of the terminal.
    * **print-styles.css** CSS style file that controls the  visual  appearance of printed pages when using the VT100 transparent printing feature.
> > It is not recommended to override the root HTML page for a particular
> > _service_. Instead, move the service to an anonymous URL and serve a
> > _static-file_ that references the _service_ in an _`<iframe>`_.
> > <p />
> > Instead of a _file_, it is possible to provide the name of a directory. This
> > turns **shellinaboxd** into a simple web server that publishes all of the files
> > in that particular directory. This option can be helpful when publishing a more
> > complex root HTML page.
  * **-g** | **--group=**_gid_
> > When started as **root**, the server drops most privileges at start up. Unless
> > overridden by the **--group** option, it switches to **nogroup**.
> > <p />
> > When already running as an unprivileged user, group changes are not possible.
> > <p />
> > If running with SSL/TLS support enabled, the certificates must be accessible to
> > the unprivileged user and/or group that the daemon runs as.
  * **-h** | **--help**
> > Display a brief usage message showing the valid command line parameters.
  * **--linkify**=`[`**none**|**normal**|**aggressive**]
> > the daemon attempts to recognize URLs in the terminal output and
> > makes them clickable. This is not neccessarily a fool-proof pro‐
> > cess and both false negatives and false positives are possible. By
> > default, only URLs starting with a well known protocol of **http**://, **https**://, **ftp**://, or **mailto**: are recognized.  In **aggressive** mode,
> > anything that looks like a hostname, URL or e-mail address is
> > recognized, even if not preceded by a protocol.
  * **--localhost-only**
> > Normally, **shellinaboxd** listens on all available network interfaces.
> > When operating behind a reverse-proxy that is not always desirable.
> > This command line option tells the daemon to only listen on the loopback
> > interface.
  * **-n** | **--numeric**
> > When running in **--verbose** mode, the daemon prints an _Apache_-style log file
> > to _stderr_. By default, host names of peers get resolved before logging them. As
> > DNS look-ups can be expensive, it is possible to request logging of numeric IP
> > addresses, instead.
  * **--pidfile=**_pidfile_
> > The  shellinaboxd  daemon  can  be configured to store its process identifier in
> > _pidfile_.

  * **-p** | **--port=**_port_
> > Unless overridden by this option, the web server listens on port 4200 for incoming
> > HTTP and HTTPS requests.
> > <p />**shellinaboxd** can distinguish between SSL/TLS requests and unencrypted
> > requests. It also knows how to negotiate **Server Name Identification**, allowing
> > the use of a single port for all types of requests even when virtual hosting.
  * **-s** | **--service=**_service_
> > One or more services can be registered on different URL paths:
> > <p />
> > > _SERVICE_ := `<url-path>` ':' _APPLICATION_

> > <p />
> > There is a pre-defined _application_, 'LOGIN', which causes the daemon to
> > invoke **/bin/login** requesting the user's name and password, and starting his
> > login shell. This is the default option for the **root** user, if no **--service**
> > was defined. Starting **/bin/login** requires **root** privileges.
> > <p />
> > There is another pre-defined _application_, 'SSH'. Instead of invoking **/bin/login**,
> > it calls **ssh**. This is the default option for unprivileged users, if no **--service**
> > was defined. This operation is available to both privileged and regular users. If the
> > optional _host_ parameter is omitted, **shellinaboxd** connects to **localhost**.
> > <p />
> > Alternatively, an _application_ can be specified by providing a
> > _user_ description, a working directory, and a command line:
> > <p />
> > > _APPLICATION_ := 'LOGIN' | 'SSH' `[` ':' `<host>` `]` | _USER_ ':' _CWD_ ':' _CMD_

> > <p />
> > The keyword 'AUTH' indicates that the _user_ information should be requested
> > interactively, instead of being provided as part of the _service_
> > description:
> > <p />
> > > _USER_ := 'AUTH' | `<username>` ':' `<groupname>`

> > <p />
> > The working directory can either be given as an absolute path, or it can be
> > the user's home directory:
> > <p />
> > > _CWD_ := 'HOME' : `<dir>`

> > <p />
> > The command that **shellinaboxd** executes can either be specified as
> > the 'SHELL' keyword, denoting the user's default login  shell,  or
> > an arbitrary command line:
> > <p />
> > > _CMD_ := 'SHELL' : `<cmdline>`

> > <p />
> > The `<cmdline>` supports expansion of variables of the form ${VAR}.
> > Supported variables are:
    * **${columns}** number of columns.
    * **${gid}**     numeric group id.
    * **${group}**   group name.
    * **${home}**    home directory.
    * **${lines}**   number of rows.
    * **${peer}**    name of remote peer.
    * **${uid}**     numeric user id.
    * **${url}**     the URL that serves the terminal session.
    * **${user}**    user name.
> > Other than the default environment variables of **$TERM**, **$COLUMNS** and **$LINES**,
> > services can have environment variables passed to them, by preceding
> > the `<cmdline>` with space separated variable assignments of the form _KEY_=_VALUE_.
> > <p />
> > The `<cmdline>` supports single and double quotes, as well as
> > backslashes for escaping characters in the familiar fashion.
> > <p />
> > Please note that when invoking **shellinaboxd** from a command line shell, additional
> > quoting might be required to prevent the shell from expanding the variables prior
> > to passing them to the daemon.
> > <p />
> > If no explicit **--service** has been requested, **shellinaboxd** defaults to
> > attaching the default service to the root directory of the web server. For **root**,
> > this is **/bin/login**, and for unprivileged users, this is **ssh localhost**. This is
> > equivalent to saying **--service=/:LOGIN**, or **--service=/:SSH**, respectively.
  * **-t** | **--disable-ssl**
> > By default, **shellinaboxd** redirectes all incoming HTTP requests to their
> > equivalent HTTPS URLs. If promoting of connections to encrypted SSL/TLS sessions
> > is undesired, this behavior can be disabled.
> > <p />
> > This option is also useful during testing or for deployment in trusted intranets,
> > if SSL certificates are unavailable.
  * **--disable-ssl-menu**
> > If the user should not be able to switch between HTTP and HTTPS modes, this choice
> > can be removed from the context menu. The user can still make this choice by directly
> > going to the appropriate URL.
  * **-q** | **--quiet**
> > Surpresses all messages to _stderr_. This option is mutually exclusive with **--debug**
> > and**--verbose**.
  * **-u** | **--user=**_uid_
> > If started as **root**, the server drops privileges by changing to **nobody**, unless
> > the _uid_ has been overridden by this option.
> > <p />
> > For more details, refer to the description of the **--group** option.
  * **--user-css=**_styles_
> > The visual appearance of the terminal emulator can be customized
> > through user-selectable style sheets. These style sheets will show
> > up as options in the right-click context menu of the terminal emulator.
> > <p />
> > Styles sheet make up either independently selectable on/off
> > options, or multiple style sheets can be grouped together. When
> > forming a group, only one member of the group can be active at any
> > given time. This is used for multiple-choice options.
> > <p />
> > Multiple independent groups are separated by semicolons:
> > <p />
> > > _STYLES_ := _GROUP_ `{` `';'` _GROUP_ `}*`

> > <p />
> > The members of a group are separated by commas:
> > <p />
> > > _GROUP_ := _OPTION_ `{` `','` _OPTION_ `}*`

> > <p />
> > Groups with exactly one member are used for options that can be
> > independently turned on and off.
> > <p />
> > Options include a human readable label that will be shown in the
> > context menu, followed by the name of the CSS file. They also must
> > include an indicator showing whether the option should initially
> > be turned on or turned off. Within a group, exactly one option
> > should be turned on:
> > <p />
> > > _OPTION_ := `<label>` `':'` `[` `'-'` `|` `'+'` `]` `<css-file>`

> > <p />
> > The user's selection of options will be persisted in a cookie.
> > This  means, the default settings of options as passed on the command
> > line only takes effect the very first time the user visits
> > the terminal emulator in his browser. On all subsequent visits,
> > the user's preferences take precedence.
  * **-v** | **--verbose**
> > Enables logging of _Apache_-style log file to _stderr_. This option is mutually
> > exclusive with **--debug** and **--quiet**.
  * **--version**
> > Prints the version number of the binary and exits.

## CONFIGURATION ##

There are no configuration files or permanent settings for **shellinaboxd**. A small
number of run-time configuration options are available from a context menu that becomes
available when clicking the right mouse button.

There are no configuration files or permanent settings for **shellinaboxd**.

A small number of run-time configuration options are available from a
context  menu that becomes available when clicking the right mouse button.
These options get persisted in a browser cookie.

Many sites already have a web server running and would  like  to  integrate **shellinaboxd** into
their existing site. This is most commonly done by means of a reverse-proxy entry for
the main web server. For _Apache_ this would require adding an option such as:
```
  <Location /shell>
    ProxyPass  http://localhost:4200/
    Order      allow,deny
    Allow      from all
  </Location>
```
If  you  are using a different web server, refer to that server's documentation
on how to configure reverse proxy operations.

When using a reverse proxy, the **--localhost-only** option would  normally
be enabled as well. In addition, the **--disable-ssl** might also be considered
depending on the exact configuration details of the reverse proxy.

## EXAMPLES ##
  * **shellinaboxd**
> > Attaches a web-enabled login shell to https://localhost:4200/. If the user connected
> > without SSL, the session will automatically be promoted. Unless SSL certificates can
> > be found in the current directory, the daemon will automatically generate suitable
> > self-signed certificates. If the command was invoked by a non-root user, the daemon
> > uses **ssh** instead of **/bin/login** for the session.
  * **shellinaboxd -t**
> > Attaches a web-enabled login shell to http://localhost:4200/ with SSL/TLS support
> > disabled.
  * **shellinaboxd -t -f beep.wav:/dev/null**
> > Runs all services with the audible-bell permanently disabled.
  * **shellinaboxd -s /:SSH:_example.org_**
> > The terminal connects to a **ssh** session on _example.org_.
  * **shellinaboxd -t -s /:AUTH:HOME:/bin/bash**
> > Interactively request the user's name and password prior to launching
> > a Bourne shell. This command can be run by unprivileged users. But if
> > doing so, it only allows this particular user to log in.
  * **shellinaboxd -c certificates -g shellinaboxd**
> > If the **certificates** directory exists and is writable by the **shellinaboxd**
> > group, self-signed SSL certificates will be generated in this directory. Running
> > this command as **root** allows any user on the system to log in
> > at http://localhost:4200/. Sessions will automatically be promoted to SSL/TLS.
  * **shellinaboxd -t -s /:LOGIN -s /who:nobody:nogroup:/:w**
> > In addition to the login shell at http://localhost:4200, show a list of currently
> > logged in users when accessing http://localhost:4200/who. This command must be run
> > as **root** in order to be able to change to **nobody**:**nogroup** as requested by
> > the service description.
  * **shellinaboxd -t -s '/:root:root:/:wy60 -c /bin/login'**
> > Instead of the standard **ANSI/VT100** terminal, publish a **Wyse 60** terminal.
> > Again, this command should be run as **root**.
  * **shellinaboxd --css white-on-black.css**
> > Loads the **white-on-black.css** style sheet from the current directory and
> > appends it to the built-in **styles.css** sheet. This causes the terminal to
> > always render white text on a black background.
  * **shellinaboxd --user-css Normal:+black-on-white.css,Reverse:-white-on-black.css**
> > Allow the user to select whether they want text to be rendered normally
> > or in reverse video. This command line option adds a new entry to the
> > right-click context menu.

## DIAGNOSTICS ##

The daemon returns a non-zero exit code in case of failure. With the
exception of a small number of common error cases that are handled
explicitly, most errors result in printing a _"Check failed"_
message. This does not typically indicate a bug in the program but is
instead its normal way of reporting errors.

Common failure conditions are reusing a port that is already in use,
lack of sufficient privileges to run a service, failure to find
SSL/TLS certificates, and failure to write newly generated
certificates to the certification directory.

## "SEE ALSO" ##
**chmod**(1), **last**(1), **login**(1), **sh**(1), **shells**(5), **openssl**(1SSL), **w**(1), **wy60**(1), **xterm**(1).

## SECURITY ##

The daemon uses privilege separation techniques to allow it to drop
privileges early. It is aware of setuid flags and restricts some
operations when launched as a setuid application.

Despite these safety features, a bug could conceivably lead to a
determined attacker gaining elevated privileges. It is therefore
strongly discouraged to set the setuid flag on the binary.

The expected deployment would be from a system _rc_ script launched
by **/sbin/init**. For extra security, the **--group** and **--user**
options should be used to change to a dedicated user.

## AUTHOR ##

Copyright (C) 2008-2009 by Markus Gutschke `<`_markus@shellinabox.com_`>`.

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License version 2 as
published by the Free Software Foundation.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307
USA

In addition to these license terms, the author grants the following
additional rights:

If you modify this program, or any covered work, by linking or
combining it with the OpenSSL project's OpenSSL library (or a
modified version of that library), containing parts covered by the
terms of the OpenSSL or SSLeay licenses, the author
grants you additional permission to convey the resulting work.
Corresponding Source for a non-source form of such a combination
shall include the source code for the parts of OpenSSL used as well
as that of the covered work.

You may at your option choose to remove this additional permission from
the work, or from any part of it.

If you would like to negotiate different licensing terms that are
compatible for integration with other projects, please contact the
author.

If the OpenSSL system libraries can be found at run-time, they will be
invoked by **shellinaboxd** to provide SSL/TLS support. The OpenSSL and SSLeay
licenses require the following notices:

This product includes software developed by the OpenSSL Project
for use in the OpenSSL Toolkit. (http://www.openssl.org/)

This product includes cryptographic software written by Eric Young
(eay@cryptsoft.com)

## BUGS ##

Due to browser limitations, some features might not be available to
users of all browers.
  * Konqueror does not allow for reliable interception of _CTRL_ keys. If you press a key together with the _CTRL_ modifier, it continues performing the browser's predefined behavior for this particular key combination. In most cases, it also fails to report the correct key to the terminal emulator. As a work-around, pressing both the _CTRL_ and the _WINDOWS_ key sometimes works.
  * Some browsers, most notably IE on Windows, disallow interception of _ALT_ keys and always interpret these keys as menu accelerators. As a work-around, many UNIX applications allow pressing _ESC_, instead of _ALT_.
  * When using non-US keyboard layouts, some browser do not allow for reliably determining shifted _ALT_ keys. Please report these cases if they turn out to be a problem, as work-arounds might be possible.
  * Access to the native clipboard is typically not possible. Instead, an internal clipboard accessible from the right-button context menu is used for all but IE.
  * Some browsers restrict the number of concurrent connections to a server. This restricts how many AJAX terminals can be opened simultaneously. If this becomes a problem, users can typically reconfigure their browsers to raise the limit.