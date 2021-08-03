# Syslog protocol

roughly conforms to the murky shade of grey known as http://www.faqs.org/rfcs/rfc3164.html

## Examples

### Manipulate packets manually

    require 'syslog_protocol_ms'

    p = SyslogProtocol::Packet.new
    p.hostname = "space_station"
    p.facility = "kern"
    p.severity = "warn"
    p.tag = "test"
    p.content = "flight control broken"
    p.to_s
    # => "<4>Aug  1 14:01:17 space_station flight control broken"
    p.pri
    # => 4
    p.facility
    # => 0
    p.facility_name
    # => "kern"
    p.severity_name
    # => "warn"
    p.warn?
    # => true
    p.info?
    # => false


### Use a Logger to generate packets

    require 'syslog_protocol_ms'

    logger = SyslogProtocol::Logger.new("space_station", "uucp")
    logger.debug("looking for uucp on board the space station")
    # => "<67>Aug  1 14:02:29 space_station looking for uucp on board the space station"
    logger.emerg("omg we cant find uucp on the space station")
    # => "<64>Aug  1 14:03:56 space_station omg we cant find uucp on the space station"


### Parse packets

    require 'syslog_protocol_ms'

    p = SyslogProtocol.parse("<34>Oct 11 22:14:15 space_station space is really getting to me")
    p.facility
    # => 4
    p.severity_name
    # => "crit"
    p.time
    # => Sun Oct 11 22:14:15 -0700 2009
    p.content
    # => "space is really getting to me"


### It yells at you for trying to abuse the protocol

    p = SyslogProtocol::Packet.new
    p.facility = 34534534
    # => ArgumentError: Facility must be within 0-23
    p.hostname = "my host"
    # => ArgumentError: Hostname may not contain spaces
    p.hostname = "h\000stname"
    # => ArgumentError: Hostname may only contain ASCII characters 33-126
    # ...etc.
    # It will also unintelligently truncate messages > 1024 bytes so beware.


## Caveats

Syslog is a terrible and loosely defined protocol. Many devices and programs do not
conform to it and so their packets may not be parsed correctly by this interpretation,
nor may the packets generated by this necessarily be recognized by other devices or programs ;)

This is probably wrong and buggy, and i know the code is ugly, thanks.

Good luck.

## TODO

* Update to more closely map to the ruby `syslog` API where possible

The MIT License (MIT)

Copyright (c) 2013 Jake Douglas and Eric Lindvall

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
