### Overview ###

Shell In A Box implements a web server that can export arbitrary command line tools to a web based terminal emulator. This emulator is accessible to any JavaScript and CSS enabled web browser and does not require any additional browser plugins. Most typically, login shells would be exported this way:

```
shellinaboxd -s /:LOGIN
```

This command starts a web server at http://localhost:4200 that allows users to login with their username and password and to get access to their login shell.

All client-server communications are encrypted, if SSL/TLS certificates have been installed.

More details are available in the [manual page](shellinaboxd_man.md).

### Demo ###

As a demonstration of the terminal emulator's capabilities, we have connected it to a minimal BASIC interpreter. This code does not require any client-server communications and runs entirely in your browser.

The BASIC interpreter is a work-in-progress. Please bear with us:

&lt;wiki:gadget url="http://shellinabox.googlecode.com/git/demo/demo.xml" width="720" height="500" border="1" /&gt;