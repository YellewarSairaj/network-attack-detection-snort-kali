# Network Attack Detection using Kali Linux and Snort

A beginner-friendly cybersecurity project that demonstrates real-time detection of ICMP ping activity using Snort IDS on Ubuntu, with GUI alerts using Zenity.

## Project Overview

This project simulates a simple attack-monitoring workflow in a lab network:

- Kali Linux acts as the attack simulator.
- Ubuntu acts as the monitoring host.
- Snort analyzes packets and detects suspicious ICMP ping traffic.
- Zenity shows immediate popup alerts when a matching Snort rule is triggered.

This is useful for understanding IDS concepts, rule-based detection, and live security monitoring.

## Architecture Diagram (Text)

```text
+-------------------+                 +-----------------------------------------+
| Kali Linux        |                 | Ubuntu Monitoring Host                  |
| (Attacker Node)   | -- ICMP Ping -->| Snort IDS Engine                        |
| ping <ubuntu-ip>  |                 | Rule: alert icmp any any -> any any ...|
+-------------------+                 | Match Event -> Console Alert -> Zenity  |
                                      +-----------------------------------------+
```

## Tools and Technologies

- Kali Linux: traffic/attack simulation
- Ubuntu Linux: monitoring environment
- Snort: intrusion detection system
- Zenity: graphical pop-up alerting
- Nano: rule editing
- Linux networking tools: ip, ping, grep

## Prerequisites

- Two machines or VMs in same network/subnet:
  - Kali Linux
  - Ubuntu
- Root/sudo access on Ubuntu
- Stable network connectivity between Kali and Ubuntu

## Installation Steps (Ubuntu)

1. Identify Ubuntu network interface and IP.

```bash
ip a
```

2. Install Snort and Zenity.

```bash
sudo apt update
sudo apt install snort zenity dbus-x11 -y
```

3. Add local ICMP detection rule.

```bash
sudo nano /etc/snort/rules/local.rules
```

Add this line:

```snort
alert icmp any any -> any any (msg:"ICMP Ping Detected"; sid:1000001; rev:1;)
```

4. Ensure local rules are included in Snort config.

```bash
sudo grep local.rules /etc/snort/snort.conf
```

Expected include line:

```conf
include $RULE_PATH/local.rules
```

## Working Procedure

1. Start Snort in console mode and pipe output for GUI notification.

```bash
sudo snort -A console -q -c /etc/snort/snort.conf -i enp0s3 | \
while read line; do
  echo "$line" | grep "ICMP Ping Detected" && \
  zenity --error --title="INTRUSION DETECTED" --text="⚠ ICMP Ping Attack Detected from Kali!"
done
```

2. From Kali machine, send ICMP traffic to Ubuntu IP.

```bash
ping <ubuntu-ip>
```

3. Observe behavior on Ubuntu:
- Snort prints alert lines in terminal.
- Zenity popup appears with intrusion warning.

## Sample Commands

### On Ubuntu (IDS)

```bash
ip a
sudo apt install snort zenity dbus-x11 -y
sudo nano /etc/snort/rules/local.rules
sudo grep local.rules /etc/snort/snort.conf
sudo snort -A console -q -c /etc/snort/snort.conf -i enp0s3
```

### On Kali (Attacker Simulation)

```bash
ping 10.253.1.162
```

## Screenshots

### 1) Ubuntu Interface and IP
![Ubuntu IP](screenshots/01-ubuntu-ip.png.jpeg)

### 2) Snort Installation
![Snort Install](screenshots/02-snort-install.png.jpeg)

### 3) Local Snort Rule
![Local Rule](screenshots/03-local-rule.png.jpeg)

### 4) Kali Ping Attack Simulation
![Kali Ping](screenshots/04-kali-ping.png.jpeg)

### 5) Intrusion Popup Alert (Zenity)
![Zenity Alert](screenshots/05-zenity-alert.png.jpeg)

## Troubleshooting

- Wrong interface in Snort command:
  - Check with `ip a` and use correct interface name.
- No popup appears:
  - Ensure `zenity` and `dbus-x11` are installed.
  - Confirm command pipe includes grep for exact message text.
- No Snort alerts:
  - Verify rule in `/etc/snort/rules/local.rules`.
  - Confirm include line exists in `/etc/snort/snort.conf`.
  - Ensure Kali can reach Ubuntu IP.
- Too many alerts:
  - Restrict rule by source/destination IPs.

## Future Scope

- Add thresholding/rate-limiting rules to reduce alert noise.
- Detect additional attacks (port scans, brute force, SYN flood patterns).
- Log alerts to files/ELK stack for analytics dashboards.
- Add email/Telegram notifications along with Zenity popup.
- Migrate to Suricata or Snort 3 with richer detection features.
- Deploy as a containerized SOC mini-lab.

## Viva Questions (Quick Practice)

1. What is the difference between IDS and IPS?
2. Why was Snort chosen for this project?
3. What does `sid` represent in a Snort rule?
4. Why do we use `-i enp0s3` in Snort command?
5. What is the role of Zenity in this setup?
6. How can false positives be reduced?

## License

For academic and educational use.
