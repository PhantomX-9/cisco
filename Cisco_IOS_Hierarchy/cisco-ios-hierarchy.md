# Cisco IOS Command Hierarchy

Cisco IOS organizes commands into **modes**. Each mode has a distinct prompt, a defined set of available commands, and a specific way to enter and exit. 

## Prompt Reference

| Mode | Prompt | Scope |
|------|--------|-------|
| User EXEC | `Router>` | View-only, basic monitoring |
| Privileged EXEC | `Router#` | Full monitoring, debugging, file ops |
| Global Configuration | `Router(config)#` | Device-wide settings |
| Interface Config | `Router(config-if)#` | Single interface |
| Subinterface Config | `Router(config-subif)#` | Logical subinterface |
| Line Config | `Router(config-line)#` | Console / VTY / AUX lines |
| Router Config | `Router(config-router)#` | Routing protocol process |
| VLAN Config | `Router(config-vlan)#` | VLAN definitions |

---

## 1. User EXEC Mode

The entry point after login. Limited to non-disruptive, view-only commands.

```
Router>
```

- Prompt ends in `>`
- Enter: log in to the device
- Exit: `logout` or `exit`
- Move up: `enable` → Privileged EXEC

Common commands: `ping`, `traceroute`, `show version`, `show clock`, `connect`, `telnet`.

---

## 2. Privileged EXEC Mode

Also called "enable mode." Grants access to all monitoring, debugging, and file-system operations. Often password-protected.

```
Router> enable
Router#
```

- Prompt ends in `#`
- Enter: `enable` from User EXEC
- Exit: `disable` (back to User EXEC) or `logout`
- Move down: `configure terminal` → Global Configuration

Common commands: `show running-config`, `show startup-config`, `copy`, `reload`, `debug`, `write memory`, `erase`.

---

## 3. Global Configuration Mode

Where device-wide configuration changes are made. The gateway to all specific configuration submodes.

```
Router# configure terminal
Router(config)#
```

- Enter: `configure terminal` (or `conf t`) from Privileged EXEC
- Exit: `exit` or `end` (or `Ctrl+Z`) → back to Privileged EXEC
- Move down: enter a specific submode (see below)

Common commands: `hostname`, `enable secret`, `ip route`, `username`, `banner`, `service`, `ip domain-name`.

---

## 4. Configuration Submodes

Entered from Global Configuration. Use `exit` to return one level up, or `end`/`Ctrl+Z` to jump straight to Privileged EXEC.

### Interface Configuration
```
Router(config)# interface GigabitEthernet0/0
Router(config-if)#
```
Configures a physical or logical interface: `ip address`, `no shutdown`, `description`, `switchport`, `speed`, `duplex`.

### Subinterface Configuration
```
Router(config)# interface GigabitEthernet0/0.10
Router(config-subif)#
```
Used for VLAN trunking / router-on-a-stick: `encapsulation dot1q`, `ip address`.

### Line Configuration
```
Router(config)# line console 0
Router(config-line)#
```
Configures console, VTY (Telnet/SSH), or AUX access lines: `password`, `login`, `transport input`, `exec-timeout`.

### Router (Routing Protocol) Configuration
```
Router(config)# router ospf 1
Router(config-router)#
```
Configures a routing process: `network`, `neighbor`, `redistribute`, `passive-interface`, `router-id`.

### VLAN Configuration
```
Switch(config)# vlan 10
Switch(config-vlan)#
```
Defines VLANs on a switch: `name`, `state`.

---

## Navigation Summary

```
User EXEC (>)
   │  enable ↓ / disable ↑
Privileged EXEC (#)
   │  configure terminal ↓ / exit ↑
Global Config (config)
   │  interface / line / router / vlan ↓
   │  exit ↑ (one level)   end or Ctrl+Z (jump to Privileged EXEC)
Specific Config Submode (config-if, config-line, config-router, ...)
```
![ios hierarchy display](ios-hierarchy-image.png)
### Key Movement Commands

| Command | Action |
|---------|--------|
| `enable` | User EXEC → Privileged EXEC |
| `disable` | Privileged EXEC → User EXEC |
| `configure terminal` | Privileged EXEC → Global Config |
| `exit` | Move up one mode |
| `end` | Jump directly to Privileged EXEC |
| `Ctrl+Z` | Same as `end` |
| `?` | Context-sensitive help (lists available commands in current mode) |

---