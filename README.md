# pySerial_example

COPY FROM https://pyserial.readthedocs.io/en/latest/examples.html

Miniterm

Miniterm is now available as module instead of example. see serial.tools.miniterm for details.

miniterm.py
    The miniterm program.
setup-miniterm-py2exe.py
    This is a py2exe setup script for Windows. It can be used to create a standalone miniterm.exe.

TCP/IP - serial bridge

This program opens a TCP/IP port. When a connection is made to that port (e.g. with telnet) it forwards all data to the serial port and vice versa.

This example only exports a raw socket connection. The next example below gives the client much more control over the remote serial port.

    The serial port settings are set on the command line when starting the program.
    There is no possibility to change settings from remote.
    All data is passed through as-is.

usage: tcp_serial_redirect.py [-h] [-q] [--parity {N,E,O,S,M}] [--rtscts]
                              [--xonxoff] [--rts RTS] [--dtr DTR]
                              [-P LOCALPORT]
                              SERIALPORT [BAUDRATE]

Simple Serial to Network (TCP/IP) redirector.

positional arguments:
  SERIALPORT            serial port name
  BAUDRATE              set baud rate, default: 9600

optional arguments:
  -h, --help            show this help message and exit
  -q, --quiet           suppress non error messages

serial port:
  --parity {N,E,O,S,M}  set parity, one of {N E O S M}, default: N
  --rtscts              enable RTS/CTS flow control (default off)
  --xonxoff             enable software flow control (default off)
  --rts RTS             set initial RTS line state (possible values: 0, 1)
  --dtr DTR             set initial DTR line state (possible values: 0, 1)

network settings:
  -P LOCALPORT, --localport LOCALPORT
                        local TCP port

NOTE: no security measures are implemented. Anyone can remotely connect to
this service over the network. Only one connection at once is supported. When
the connection is terminated it waits for the next connect.

tcp_serial_redirect.py
    Main program.

Single-port TCP/IP - serial bridge (RFC 2217)

Simple cross platform RFC 2217 serial port server. It uses threads and is portable (runs on POSIX, Windows, etc).

    The port settings and control lines (RTS/DTR) can be changed at any time using RFC 2217 requests. The status lines (DSR/CTS/RI/CD) are polled every second and notifications are sent to the client.
    Telnet character IAC (0xff) needs to be doubled in data stream. IAC followed by another value is interpreted as Telnet command sequence.
    Telnet negotiation commands are sent when connecting to the server.
    RTS/DTR are activated on client connect and deactivated on disconnect.
    Default port settings are set again when client disconnects.

usage: rfc2217_server.py [-h] [-p TCPPORT] [-v] SERIALPORT

RFC 2217 Serial to Network (TCP/IP) redirector.

positional arguments:
  SERIALPORT

optional arguments:
  -h, --help            show this help message and exit
  -p TCPPORT, --localport TCPPORT
                        local TCP port, default: 2217
  -v, --verbose         print more diagnostic messages (option can be given
                        multiple times)

NOTE: no security measures are implemented. Anyone can remotely connect to
this service over the network. Only one connection at once is supported. When
the connection is terminated it waits for the next connect.

New in version 2.5.

rfc2217_server.py
    Main program.
setup-rfc2217_server-py2exe.py
    This is a py2exe setup script for Windows. It can be used to create a standalone rfc2217_server.exe.

Multi-port TCP/IP - serial bridge (RFC 2217)

This example implements a TCP/IP to serial port service that works with multiple ports at once. It uses select, no threads, for the serial ports and the network sockets and therefore runs on POSIX systems only.

    Full control over the serial port with RFC 2217.
    Check existence of /tty/USB0...8. This is done every 5 seconds using os.path.exists.
    Send zeroconf announcements when port appears or disappears (uses python-avahi and dbus). Service name: _serial_port._tcp.
    Each serial port becomes available as one TCP/IP server. e.g. /dev/ttyUSB0 is reachable at <host>:7000.
    Single process for all ports and sockets (not per port).
    The script can be started as daemon.
    Logging to stdout or when run as daemon to syslog.
    Default port settings are set again when client disconnects.
    modem status lines (CTS/DSR/RI/CD) are not polled periodically and the server therefore does not send NOTIFY_MODEMSTATE on its own. However it responds to request from the client (i.e. use the poll_modem option in the URL when using a pySerial client.)

usage: port_publisher.py [options]

Announce the existence of devices using zeroconf and provide
a TCP/IP <-> serial port gateway (implements RFC 2217).

If running as daemon, write to syslog. Otherwise write to stdout.

optional arguments:
  -h, --help            show this help message and exit

serial port settings:
  --ports-regex REGEX   specify a regex to search against the serial devices
                        and their descriptions (default: /dev/ttyUSB[0-9]+)

network settings:
  --tcp-port PORT       specify lowest TCP port number (default: 7000)

daemon:
  -d, --daemon          start as daemon
  --pidfile FILE        specify a name for the PID file

diagnostics:
  -o FILE, --logfile FILE
                        write messages file instead of stdout
  -q, --quiet           suppress most diagnostic messages
  -v, --verbose         increase diagnostic messages

NOTE: no security measures are implemented. Anyone can remotely connect to
this service over the network. Only one connection at once, per port, is
supported. When the connection is terminated, it waits for the next connect.

Requirements:

    Python (>= 2.4)
    python-avahi
    python-dbus
    python-serial (>= 2.5)

Installation as daemon:

    Copy the script port_publisher.py to /usr/local/bin.
    Copy the script port_publisher.sh to /etc/init.d.
    Add links to the runlevels using update-rc.d port_publisher.sh defaults 99
    That’s it :-) the service will be started on next reboot. Alternatively run invoke-rc.d port_publisher.sh start as root.

New in version 2.5: new example

port_publisher.py
    Multi-port TCP/IP-serial converter (RFC 2217) for POSIX environments.
port_publisher.sh
    Example init.d script.

wxPython examples

A simple terminal application for wxPython and a flexible serial port configuration dialog are shown here.

wxTerminal.py
    A simple terminal application. Note that the length of the buffer is limited by wx and it may suddenly stop displaying new input.
wxTerminal.wxg
    A wxGlade design file for the terminal application.
wxSerialConfigDialog.py
    A flexible serial port configuration dialog.
wxSerialConfigDialog.wxg
    The wxGlade design file for the configuration dialog.
setup-wxTerminal-py2exe.py
    A py2exe setup script to package the terminal application.

Unit tests

The project uses a number of unit test to verify the functionality. They all need a loop back connector. The scripts itself contain more information. All test scripts are contained in the directory test.

The unit tests are performed on port loop:// unless a different device name or URL is given on the command line (sys.argv[1]). e.g. to run the test on an attached USB-serial converter hwgrep://USB could be used or the actual name such as /dev/ttyUSB0 or COM1 (depending on platform).

run_all_tests.py
    Collect all tests from all test* files and run them. By default, the loop:// device is used.
test.py
    Basic tests (binary capabilities, timeout, control lines).
test_advanced.py
    Test more advanced features (properties).
test_high_load.py
    Tests involving sending a lot of data.
test_readline.py
    Tests involving readline.
test_iolib.py
    Tests involving the io library. Only available for Python 2.6 and newer.
test_url.py
    Tests involving the URL feature.

