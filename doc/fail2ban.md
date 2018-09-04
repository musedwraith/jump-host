# Fail2ban

[Fail2ban](https://www.fail2ban.org/wiki/index.php/Main_Page) is a tool that scans log files and bans IP address(es) based on selective filter rules.

In short, it acts as a firewall against failed login attempts.

The focus of this document will be utilizing Fail2ban to secure a bastion host / jumpbox.

## How it works

Fail2ban is a set of python application running on [TOML based](https://github.com/toml-lang/toml) configuration files.

It employs one of the following methods (pyinotify, gamin, polling) to periodically _filter_ log files (/var/log/secure && /var/log/messages) against [regular expressions](https://www.regular-expressions.info/). Upon identifying a match in the logs, it will take an _action_ based on configurations.

Most commonly, it will trigger an IP ban using an IP table for a period of time.

## Setup

## Configuration

Fail2Ban uses three primary files for configuration.

A `jail.conf` file control the type of _jail_ to enable and the filters and actions to use.

A `filter.conf` file is a set of regular expressions to match against to take action on.

A `action.conf` file can simply be a set of iptables commands to block particular traffic.

### Where you can find them

| Jail                     | Filter                   | Action                                 |
| ------------------------ | ------------------------ | -------------------------------------- |
| /etc/fail2ban/jail.local | /etc/fail2ban/sshd.local | /etc/fail2ban/iptables-multiport.local |

### Iptables

Iptables is a user-space program commonly used as a firewall in Linux systems. The tables in iptables consist of _chains_, lists of rules followed in order. Every chain has a unique name.

Fail2ban use names with prefixes of `fail2ban-` in iptables.
A Fail2ban action to drop all traffic from a particular IP address: `iptables -I fail2ban-<name> 1 -s <ip> -j DROP` requires the name of the chain to work properly with Fail2ban.

#### Useful commands

`sudo fail2ban-client -vvv -x start` - Debugging configuration files for fail2ban.

`iptables -L` - list all iptable chains.

A larger list of commands can be found [here](https://wiki.archlinux.org/index.php/iptables).