# Auditd Overview

<p>This document acts as my exploration and understanding of Auditd, a background daemon that tracks and logs system events.</p>

- [Why Use Auditd?](#why-use-auditd?)
- [Auditd Fundamentals](#learning-auditd-fundamentals)
    - [Viewing Logs](#viewing-logs)
    - [Watch Rules](#watch-rules)
- [Monitoring Created Events](#monitoring-created-events)

## Why Use Auditd?

Auditd, much like Wireshark, is a free, open-source software that is heavily utilised due to how powerful it is (as you can get very close to the processes within your computer).

It's main strength is to collect security-related events into a centralised audit log, which can be extremely useful when monitoring the security events of your device and network.

## Learning Auditd Fundamentals

Auditd seems to be based around preconfigured rules and properties, which affect the generation of the log entries via Auditd.

Installing it and starting the Auditd service was relatively straightforward via my VM terminal:

> sudo dnf install audit
> sudo service auitdid start

I can then easily check the current auditd status via:

> sudo auditctl -s

Which produced the following terminal output:



I can determine it is running via the 'enabled' setting being set to 1 (true).

After installing and activating the daemon, I began investigating how to actually view the audit logs, which are typically protected.

### Viewing Logs

Viewing logs can be done using _ausearch_ which searches the audit.log file by default.
An example output of using ausearch is below:



As can be observed, it prints a variety of logs which includes date, time (down to the second!) and "type".
I will investigate how to properly investigate logs later in this overview- as of right now I am getting my bearings of accessing each section of the daemon.

### Watch Rules

Watch rules track whether a file or directory is accessed (including read and write changes). This effectively acts as a filter for the logs. The common syntax to define these rules are:

> auditdctl -w path_to_file -p permissions -k key_name

I will later apply these by adding monitoring rules, triggering their associated events, and collecting the logs.

## Monitoring Created Events

This section covers the events which I created myself and then monitored using auditd to launch an investigation.

### Monitoring Passwd Changes
This simulates a privelege-related event, which should be treated as high priority in terms of systems security.

#### Simulating Event

First, I implemented a rule for the daemon to track changes in the passw file, tagging these events as 'passwd_changes':

> sudo auditctl -w /etc/passwd -p wa -k passwd_changes

Then I have to trigger the event. I won't actually be changing my password, as just entering the same password will trigger the system call:

> sudo passwd dant

This event should now be stored under the label 'passwd_changes' within the logs kept by auditd- which I'll observe in the next section.

#### Observing Logs

In order to observe the logs, I will use _ausearch_ which queries daemon logs:

> sudo ausearch -k passwd_changes

And from this search I had the following results:




To me, as of now, I am only able to observe basic parts of the log:

- The exact date and time the action was performed (under the 'time->' heading)
- Whether the action successfully ran (under the 'success' heading)
- Three different action types which are includes (under the 'type' heading which included SYSCALL and CONFIG_CHANGE).

After reading the log, I realised I don't yet properly understand how to best start analyzing the log itself; and will fully investigate this process for the next observation I make.

### Monitoring SSH Logins

Now I'l track authentication events by adding rules to watch my authentication logs:

> sudo auditctl -w /var/log/auth.log -p wa -k authlog

Which means any attribute change events inside of the auth.log file should be tagged as authlog.
From my local Windows computer, I begin both a successful SSH Login and a failed SSH login, as shown below:

#### Simulating Event

I began by simulating a successful SSH via my local machine. After successfully logging into my VM, as proved below:



Then I logged out to perform a failed SSH login via incorrect password. I entered the password incorrectly to observe what difference would be made within the logs:



#### Observing Logs

Neither the correct or incorrect login appeared. At first I believed the failed login never reached my VM machine, instead the login would get caught on a "middle-man" between my local machine and VM, so Auditd would never be able to observe a failed login. However, by using _sudo tail_ I noticed that the failed login attempt was still coming through. This meant that Auditd wasn't properly catching the login.

After further investigation, I found that the logs were being sent toward a journald-first logging pipeline as opposed to auth.log (where I was searching), which meant I was searching for logs in a place that they weren't stored, which explains why only the defining of the rule was shown as opposed to any events associated with the rule.

However, by viewing them via journalctl I can be certain that they have come through:



So why journald viewing rather than auditd rule? After searching, I found that journald logging is the default on modern Ubuntu.Auditd cannot see journald events because journald isn't a file.

I searched further and found that I could install ryslog to forward logs to auth.log instead, but I decided not to as I have already gotten a suitable understanding of the architecture and have confirmed that I can monitor failed SSH login attempts (even if not using Auditd).

### Brute-Force Detection

This continues from my previous event simulation, but instead of running a few SSH logins, I'll run hundreds at a time. I was going to observe this purely via Auditd, but considering I won't be using Auditd, and how much I'm looking forward to it, I've decided to make it encapsulate this project by observing it via Wireshark too!

As such, I'll focus on this event in the Project Overview document so I can use skills from both Auditd, SSH logins from my local machine, and Wireshark to see a brute-force attack from a packet level.
