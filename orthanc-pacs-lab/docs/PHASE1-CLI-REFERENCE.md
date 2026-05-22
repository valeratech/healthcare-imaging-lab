# Phase 1 — Complete CLI Command Reference

A reference document capturing every CLI command executed during Phase 1,
organized by functional category. Each entry includes the command, what it
does, and why it was executed in this lab context.

## System and Network Verification

**`hostnamectl`**
Displays the system hostname, operating system, kernel version, and
virtualization platform. Executed post-install to confirm the hostname
was set correctly to `orthanc-primary` and that VMware virtualization
was detected by the OS.

**`ip link show`**
Displays all network interfaces and their link state (UP/DOWN) without
showing IP addresses. Executed post-install to confirm both NICs were
visible to the OS — ens33 (VMnet8 NAT) and ens34 (VMnet2 PACS segment).

**`ip addr show ens33`**
Displays the full network configuration of a specific interface including
IP address, broadcast, and lease information. Executed to retrieve the
DHCP-assigned NAT IP address needed to establish the initial SSH session
from rhcontrol.

**`ip addr show ens34`**
Same as above but targeted at the PACS segment interface. Executed
post-Netplan apply to confirm the static IP `192.168.100.10/24` was
correctly assigned and the interface was UP.

**`ip a`**
Shorthand for `ip addr show` — displays all interfaces and their full
address configuration simultaneously. Executed from the VMware console
to get a full network state snapshot before SSH was established.

**`timedatectl`**
Displays system clock status including local time, UTC time, NTP
synchronization state, and RTC configuration. Executed to diagnose the
Ubuntu archive mirror release file validation error — confirmed clock
was accurate and NTP was active.

## Package Management

**`sudo apt update`**
Refreshes the local package index from all configured repositories.
Executed before every package installation to ensure the latest available
versions are pulled.

**`sudo apt upgrade -y`**
Applies all available package upgrades non-interactively. Executed after
initial OS install to bring the system fully current before installing
any lab software.

**`sudo apt install iputils-ping -y`**
Installs the `ping` utility. Not included in Ubuntu 24.04 minimized
server install. Executed to enable basic network connectivity testing
from the server.

**`sudo apt install dnsutils -y`**
Installs DNS utilities including `nslookup` and `dig`. Not included in
minimized install. Executed to enable DNS resolution testing —
specifically to diagnose the `package.orthanc-server.com` resolution
failure.

**`sudo apt install curl -y`**
Installs the curl HTTP client. Already present on this system but
included in the install sequence as a pre-requisite check. Used for
GPG key retrieval and REST API validation.

**`sudo apt install orthanc orthanc-postgresql -y`**
Installs the Orthanc DICOM server and its PostgreSQL plugin from the
Ubuntu universe repository. The core lab software installation command.

**`sudo apt install postgresql postgresql-contrib -y`**
Installs PostgreSQL database server and contributed extensions. Executed
in Stage 4 to deploy the mandatory PostgreSQL backend before Orthanc
configuration.

**`apt-cache show orthanc | grep Version`**
Queries the local package cache for available Orthanc versions without
installing anything. Executed to confirm what version was available in
the Ubuntu universe repository before deciding to proceed with that
package rather than waiting for the upstream repository to resolve.

**`dpkg -l orthanc`**
Queries the dpkg package database to confirm a package is installed and
display its version and status. Executed to verify Orthanc was correctly
installed since the `orthanc --version` command is not available —
Orthanc runs as a service, not a direct binary.

**`dpkg -l orthanc-postgresql`**
Same as above for the PostgreSQL plugin package. Executed to confirm
plugin installation independently of the core package.

## File System and Configuration

**`ls /etc/orthanc/`**
Lists the contents of the Orthanc configuration directory. Executed to
confirm all expected configuration files were present after installation
— orthanc.json, postgresql.json, credentials.json, worklists.json,
serve-folders.json.

**`ls /usr/share/orthanc/plugins/`**
Lists the installed Orthanc plugin shared libraries. Executed to confirm
both PostgreSQL plugins and all bundled plugins were present and
available for loading.

**`ls /var/log/orthanc/`**
Lists the Orthanc log directory contents. Executed to confirm the log
directory was created and active log files were being written.

**`ls /var/lib/orthanc/`**
Lists the Orthanc storage directory. Executed to confirm the default
DICOM file storage path `db-v6` was created.

**`sudo cat /etc/orthanc/orthanc.json`**
Displays the full Orthanc main configuration file. Executed to review
the complete default configuration before making targeted edits.

**`sudo cat /etc/orthanc/credentials.json`**
Displays the Orthanc authentication credentials file. Executed to review
the default structure before configuring the admin user.

**`sudo cat /etc/orthanc/postgresql.json`**
Displays the PostgreSQL plugin configuration file. Executed to review
default values before configuring the database connection parameters.

**`sudo cat /etc/netplan/50-cloud-init.yaml`**
Displays the cloud-init generated Netplan configuration. Executed to
understand the existing network configuration before adding the static
IP configuration for ens34.

**`sudo grep -n "Name\|DicomAet\|RemoteAccessAllowed\|AuthenticationEnabled" /etc/orthanc/orthanc.json`**
Searches orthanc.json for specific configuration keys and displays their
line numbers and values. Executed to verify the four targeted
configuration changes were correctly applied without having to read the
entire 700-line file.

**`sudo nano /etc/orthanc/orthanc.json`**
Opens the Orthanc main configuration file in the nano text editor with
elevated privileges. Executed to make the four targeted configuration
changes — Name, DicomAet, RemoteAccessAllowed, AuthenticationEnabled.

**`sudo nano /etc/orthanc/credentials.json`**
Opens the credentials file in nano. Executed to configure the admin
username and password for REST API and Orthanc Explorer authentication.

**`sudo nano /etc/orthanc/postgresql.json`**
Opens the PostgreSQL plugin configuration in nano. Executed to configure
the database connection — EnableIndex, database name, username,
and password.

**`sudo nano /etc/netplan/99-pacs-static.yaml`**
Creates and opens a new Netplan configuration file in nano. Executed to
define the static IP assignment for ens34 on the PACS segment without
modifying the cloud-init managed file.

**`sudo chmod 600 /etc/netplan/99-pacs-static.yaml`**
Sets file permissions to owner read/write only. Executed because Netplan
enforces strict permission requirements on configuration files — files
with world-readable permissions are rejected with a warning.

**`sudo tail -50 /var/log/orthanc/Orthanc.log`**
Displays the last 50 lines of the Orthanc log file. Executed after
service restart to confirm clean startup, PostgreSQL plugin loading,
DICOM server initialization, and HTTP server binding — more detailed
than journalctl for Orthanc-specific events.

**`cat /etc/os-release`**
Displays the operating system identification file. Executed from the SSH
session to confirm the OS version on orthanc-primary from the rhcontrol
management node.

## Service Management

**`sudo systemctl status orthanc`**
Displays the current state of the Orthanc systemd service including
active/inactive state, PID, memory usage, and recent log entries.
Executed after install and after every restart to confirm clean service
state.

**`sudo systemctl status postgresql`**
Displays the state of the PostgreSQL parent service unit. Executed
post-install to confirm PostgreSQL was installed and enabled — shows
`active (exited)` which is normal for the wrapper unit.

**`sudo systemctl status postgresql@16-main`**
Displays the state of the specific PostgreSQL cluster service. Executed
to confirm the actual database cluster was running — this is the service
that matters operationally, not the parent wrapper.

**`sudo systemctl restart orthanc`**
Restarts the Orthanc service. Executed after each configuration file
edit to apply changes.

**`sudo systemctl enable orthanc`**
Enables Orthanc to start automatically at boot. Confirmed already
enabled by the package installer.

**`sudo journalctl -u orthanc -n 50`**
Displays the last 50 systemd journal entries for the Orthanc service.
Executed after restart to check for service-level errors — complements
the Orthanc log file which contains application-level detail.

**`sudo netplan apply`**
Applies all Netplan network configuration files without requiring a
reboot. Executed after creating `99-pacs-static.yaml` to bring ens34
up with the static IP assignment immediately.

## PostgreSQL Administration

**`sudo -u postgres psql`**
Opens an interactive PostgreSQL session as the postgres superuser by
switching to the postgres system user. Executed to create the Orthanc
database and user — requires superuser privileges not available to the
valeratech account.

**`CREATE USER orthanc WITH PASSWORD '...';`**
PostgreSQL DDL command to create a dedicated database user. Executed to
create the service account Orthanc uses to authenticate to PostgreSQL —
follows principle of least privilege, not using the postgres superuser.

**`CREATE DATABASE orthanc OWNER orthanc;`**
PostgreSQL DDL command to create the Orthanc database owned by the
orthanc user. Executed to provision the database that the PostgreSQL
index plugin writes to.

**`GRANT ALL PRIVILEGES ON DATABASE orthanc TO orthanc;`**
PostgreSQL DCL command to grant full privileges on the database to the
orthanc user. Executed to ensure Orthanc can create tables, write
indexes, and manage schema on startup.

**`\l`**
PostgreSQL meta-command to list all databases with their owners,
encoding, and access privileges. Executed to verify the orthanc database
was created correctly with the correct owner and privilege assignments.

**`\q`**
PostgreSQL meta-command to exit the interactive psql session. Executed
after completing database provisioning.

**`psql -U orthanc -h localhost -d orthanc -c "SELECT current_database(), current_user;"`**
Connects to the orthanc database as the orthanc user via TCP on localhost
and executes a verification query. Executed to confirm the connection
string Orthanc will use — validates username, password, database name,
and TCP connectivity before configuring the plugin.

**`sudo -u postgres psql -c "SELECT version();"`**
Executes a PostgreSQL version query as the postgres superuser. Executed
to confirm the installed PostgreSQL version and that the cluster was
accepting connections immediately after installation.

## Network Connectivity Testing

**`ping -c 3 8.8.8.8`**
Sends 3 ICMP echo requests to Google's public DNS server. Executed to
verify basic IP connectivity from orthanc-primary — confirms the NAT
network path to the internet is functional independent of DNS.

**`ping -c 3 google.com`**
Sends 3 ICMP echo requests to google.com requiring DNS resolution first.
Executed to verify both DNS resolution and IP connectivity simultaneously
— confirmed both were working after the Orthanc repository DNS failure.

**`nc -zv 192.168.175.128 4242`**
Uses netcat to test TCP connectivity to a specific host and port without
sending data. The `-z` flag scans without sending data, `-v` enables
verbose output. Executed from rhcontrol to confirm Orthanc's DICOM port
4242 was open and accepting TCP connections.

**`curl -fsSL https://package.orthanc-server.com/orthanc.gpg | sudo gpg --dearmor -o /usr/share/keyrings/orthanc.gpg`**
Downloads the Orthanc repository GPG signing key and converts it from
ASCII armor format to binary keyring format. Executed as part of the
upstream Orthanc repository setup attempt — failed due to DNS resolution
failure for `package.orthanc-server.com`.

**`curl -u admin:password http://192.168.175.128:8042/system`**
Issues an authenticated HTTP GET request to the Orthanc REST API system
endpoint. Executed from rhcontrol to validate the full stack — confirms
REST API is reachable, authentication is enforced and working, and
returns the system c
