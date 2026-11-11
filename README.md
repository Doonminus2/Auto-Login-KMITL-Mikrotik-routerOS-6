# KMITL Auto-Login for MikroTik RouterOS 6
 
A MikroTik script that keeps a RouterOS **v6** router logged in to the KMITL network.
 
This is a port of [CE-HOUSE/Auto-Login-KMITL-Mikrotik](https://github.com/CE-HOUSE/Auto-Login-KMITL-Mikrotik), which needs RouterOS v7.13+ because it parses JSON with `:deserialize`. Use the upstream script if your router runs v7.
 
| :warning: **Disclaimer:** This project is only an experiment on the KMITL authentication system and does not bypass the login system. You still log in with your own KMITL account. |
| --- |
 
## What changed from upstream
 
1. `ParseJSON` is rewritten with `:find` and `:pick`, so it runs on v6.
2. A login counts as successful only when the reply contains `"success":true`. An HTML error page is now logged as a failure.
3. Leaving the IP empty during setup makes the script use the WAN IP that the DHCP client got on `ether1`.
Login endpoint, payload, heartbeat and schedulers are the same as upstream.
 
## Requirements
 
- MikroTik RouterOS **v6.43 or later** (tested target: 6.45.9 on RB951Ui-2nD)
- `ether1` is the WAN port connected to the KMITL network
## Install
 
The router cannot reach GitHub before it is logged in, so copy the file to the router first.
 
1. Download `Auto-Login-KMITL-v6.rsc` to your computer.
2. Upload it to the router (or drag it into Winbox → Files):
```bash
   scp Auto-Login-KMITL-v6.rsc admin@x.x.x.x:/
```
 
3. Open a terminal on the router and import it:
```
   /import file-name=Auto-Login-KMITL-v6.rsc
```
 
4. On first install the script asks for:
   - **Username**: Student/Staff ID without `@kmitl.ac.th`
   - **Password**: masked input
   - **IP**: press Enter without typing to use the WAN IP from DHCP
If another device on your network already runs an auto-login script, stop it first so the two do not log in against each other.
 
## Usage
 
The script creates three schedulers:
 
- **AutoLogin-AutoStart**: runs on boot and logs in once the link is up.
- **AutoLogin-Heartbeat**: runs every minute to keep the session alive.
- **AutoLogin-AutoReLogin**: checks every 9 hours and logs in again if the session expired.
View logs:
 
```
/log print where message~"Auto-Login"
```
 
## Config
 
Change settings later in **System → Scripts → `AutoLogin-Config`**.
 
| Name | Description |
| :---: | --- |
| `username` | Username without `@kmitl.ac.th` |
| `password` | Password |
| `ipaddress` | WAN IP. Leave as `""` to use the IP from DHCP on `ether1` |
 
> **Security:** the password is stored in plain text in `AutoLogin-Config` and appears in `/export` output. Remove that line before sharing an export or committing it anywhere.
 
## Troubleshooting
 
- **`Network Error`**: the router could not reach `portal.kmitl.ac.th:19008`. Check that `ether1` has an IP and that DNS works.
- **`Can not login... server-msg: ...`**: the portal rejected the login. Check username and password in `AutoLogin-Config`.
- **`Can not login... server-msg:` with no message**: the portal replied with something that is not JSON (often an HTML page). Please open an issue with the log line.
## Credit
 
- [CE-HOUSE/Auto-Login-KMITL-Mikrotik](https://github.com/CE-HOUSE/Auto-Login-KMITL-Mikrotik): the v7 script this port is based on, and most of this README
- [@ouoam](https://github.com/ouoam) for [KMITL-Auto-Authen-Mikrotik](https://github.com/ouoam/KMITL-Auto-Authen-Mikrotik), the original RouterOS script
- Network Laboratory members for [Auto Authen KMITL](https://gitlab.com/networklab-kmitl/auto-authen-kmitl) (Python)
- [@mayueeeee](https://github.com/mayueeeee) for [KMITL-Auto-Authen](https://github.com/mayueeeee/KMITL-Auto-Authen) (Go)
