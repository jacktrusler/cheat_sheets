# Networking

(most of this was taken from u/8483 networkingTools.md from [this thread](https://www.reddit.com/r/learnprogramming/comments/106jfin/my_selftaught_programming_notes_fullstack_web/))

```bash
sudo dnf update
sudo dnf install netcat nmap wireshark-cli
```
Other useful networking programs baked into Fedora.
`tcpdump | traceroute | mtr`

| unix             | windows  | use                                                         |
| ---------------- | -------- | ----------------------------------------------------------- |
| ifconfig / route | ifconfig | View the system's network configuration                     |
| grep             | findstr  | Search for specific text                                    |
| netstat          | netstat  | Display established network connections and statistics      |
| lsof             |          | What processes open which files                             |
| route            |          | Display where and change how traffic is sent                |
| tcpdump          |          | Display traffic to and from a server, view network activity |
| traceroute       | tracert  | Show the route the traffic takes                            |
| host             | nslookup | Explore the Domain Name System (DNS)                        |

## Workflow

1. Find IP address of PC.

```bash
ip addr
ip -c addr

# IP address: 192.168.125.252/24
# /24 = 255.255.255.0 subnet mask
```

2. Scan subnet for other devices (IP addresses).

```bash
nmap -sn 192.168.125.252/24
```

3. Ping other device.
ping sends individual packets to test if traffic can get from one address to another, and back.

```bash
# send 5 packets to device on network
ping -c 5 192.168.125.204
# Google DNS
ping 8.8.8.8
ping google.com #142.251.167.138
```

4.  Default gateway (router)

```
ip route show
route -n
netstat -rn
```

## Interfaces

```
ls /sys/class/net/
netstat -i
ip link show
```

```bash
#### unknown host
sudo vim /etc/resolv.conf
# Add nameserver 8.8.8.8
```

## lsof

The `lsof` utility lists open files, including network sockets (listening or connected).

```bash
# List only network sockets
lsof -i
```

## nc / netcat

`netcat` is a tool for manually talking to servers, by connecting to a port and sending a string over it. It's a thin wrapper over TCP.

```bash
nc en.wikipedia.org 80
nc localhost 22
nc gmail-smtp-in.l.google.com 80
```

To illustrate, we can use two terminals to talk to each other. This is a simple TCP server.

**Anything typed at the second console will be concatenated to the first, and vice-versa.**

The connection is closed with `CTRL` + `d`.

```bash
# terminal 1 - Listen for an incoming connection on port 6666
nc -l 6666
# typed: foo
# shown: bar

# terminal 2 - Connect to the machine on that listening port
nc 127.0.0.1 6666
# show: foo
# typed: bar
```

Commands can be sent via a `pipe`.

```bash
echo 'message' | netcat server 80
```

`netcat` doesn't know anything about forming HTTP request, but in combination with `printf` and `piping`, it can be done.

```bash
# Google
printf 'HEAD / HTTP/1.1\r\nHost: google.com\r\n\r\n' | nc google.com 80

# JSON
printf 'GET /posts/1 HTTP/1.1\r\nHost:jsonplaceholder.typicode.com\r\n\r\n' | nc jsonplaceholder.typicode.com 80
```

## host

Used for looking up records in the DNS.

```bash
# Returns all the records
host google.com

# Returns just the A record
host -t a google.com
```

## dig
**D**igital **I**nformation **G**roper
Similar to `host` in showing DNS records, but in a way more readable for scripts and closer to the way they are stored in the DNS configuration files.

```bash
dig google.com
```

## tshark

The terminal version of Wireshark, used for packet sniffing. Hard to read the default output, but can be formatted.
```bash
tshark -i wlan0 -c 10 -T fields -e ip.src -e ip.dst -e ip.proto -e tcp.srcport -e tcp.dstport -e udp.srcport -e udp.dstport > test.text
```
