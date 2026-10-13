*This project has been created as part of the 42 curriculum by marcebar.*

## Description
Born2beRoot is a system administration project whose goal is to set up a secure server inside a virtual machine, following strict rules, and to understand how the main building blocks of a Linux server fit together: partitioning, encryption, users and groups, password policies, privileged access, firewalling, mandatory access control, services and monitoring.

-> The main elements of the setup are:
 
- Encrypted LVM partitioning (LUKS).
- An SSH server listening only on port **4242**, with root login disabled.
- A firewall (**UFW**) allowing only the required port(s).
- **AppArmor** active at boot and enforcing profiles.
- A strong password policy (aging + complexity rules).
- A strict **sudo** configuration (limited attempts, custom error message, input/output logging, TTY required, restricted `secure_path`).
- A hostname of the form `marcebar42` and a user belonging to the `user42` and `sudo` groups.
- A `monitoring.sh` script, run every 10 minutes by `cron`, broadcasting system information to all terminals via `wall`.

-> Choice of operating system: Debian

I chose Debian because it is the more beginner-friendly of the two allowed options, and the subject also recommends it for beginners. It has a very large community, extensive documentation, a simple installer (with guided LVM + encryption support), a stable release model and a vast package repository. Its tooling (APT, UFW, AppArmor) is also simpler to learn for a first server-administration project.

### Comparisons
 
#### Debian vs Rocky Linux
 
| | **Debian** | **Rocky Linux** |
|---|---|---|
| Family | Independent, community-driven; base of Ubuntu | RHEL-compatible (successor of CentOS), community-driven |
| Package manager | `apt` / `dpkg` (`.deb`) | `dnf` / `rpm` (`.rpm`) |
| Default MAC | AppArmor | SELinux |
| Default firewall tool | UFW / nftables (UFW is installed manually) | firewalld |
| Release model | Stable releases every ~2 years, very conservative | Follows RHEL: ~10 years of support per major version |
| Software freshness | Older but very stable packages in `stable` | Also conservative and enterprise-oriented |
| Typical use | General servers, desktops, containers, learning | Enterprise servers, environments requiring RHEL compatibility |
| **Advantages** | Easy to learn, huge documentation and community, huge repository, simple installer | Enterprise-grade, long support cycle, strong default security (SELinux), RHEL-compatible ecosystem |
| **Disadvantages** | Shorter default support window per release; AppArmor is less granular than SELinux | Steeper learning curve (SELinux, firewalld), smaller community for beginners, fewer packages in default repos |
 
#### AppArmor vs SELinux
 
| | **AppArmor** | **SELinux** |
|---|---|---|
| Model | Path-based: profiles restrict what a program can access by file path | Label-based: every file, process and port has a security context |
| Complexity | Simpler, easier to read and write profiles | Much more complex and granular |
| Default on | Debian, Ubuntu, SUSE | RHEL, Rocky, Fedora, CentOS |
| Granularity | Good, per-application | Very fine, system-wide policy |
| Learning curve | Gentle | Steep |
| Typical weakness | Path-based rules can be bypassed with hard links / renamed paths in some setups | Misconfiguration often leads people to disable it |
 
#### UFW vs firewalld
 
| | **UFW** (Uncomplicated Firewall) | **firewalld** |
|---|---|---|
| Philosophy | Simple front-end for `iptables`/`nftables` | Dynamic firewall manager with zones |
| Configuration | Simple rules: `ufw allow 4242/tcp` | Zones and services: `firewall-cmd --add-port=4242/tcp` |
| Runtime changes | Rules applied and persisted directly | Distinguishes runtime and permanent config |
| Default on | Ubuntu / Debian (installed manually on Debian) | RHEL, Rocky, Fedora |
| Best for | Single hosts, simple setups | Servers with multiple interfaces/zones, complex setups |
 
#### VirtualBox vs UTM
 
| | **VirtualBox** | **UTM** |
|---|---|---|
| Developer | Oracle (open source core) | Open source, built on QEMU |
| Platforms | Windows, Linux, macOS (Intel) | macOS (Intel and Apple Silicon), iOS |
| Architecture support | x86/x86_64 hosts | Apple Silicon (virtualization) + emulation of other architectures |
| Performance | Good on x86 hosts | Near-native on Apple Silicon, slower when emulating |
| Ease of use | Mature, many features (snapshots, shared folders) | Simple interface, fewer advanced features |
| Why choose it | Works on the school machines and most PCs | The only practical choice on Apple Silicon Macs |
 

---
## Instructions
### Requirements
 
- <VirtualBox / UTM> installed.
- Debian ISO (`debian-<version>-amd64-netinst.iso`) from <https://www.debian.org/distrib/>.
- At least <X> GB of free disk space.
### Installation
 
1. Create a new VM in <VirtualBox / UTM> (Linux / Debian 64-bit, <RAM> MB RAM, <disk size> GB disk).
2. Boot from the Debian ISO and run the **non-graphical install**.
3. Set the hostname to `<login>42`, create the user `<login>` and set strong passwords for root and the user.
4. Use **guided partitioning with encrypted LVM** (or manual partitioning following the layout above).
5. Choose only the *SSH server* and *standard system utilities* (no desktop environment).
6. Finish the installation, then configure sudo, SSH (port 4242), UFW, password policy and the monitoring script.
### Execution
 
1. Start the VM and enter the disk-encryption passphrase.
2. Log in with your user.
3. Connect via SSH from the host (port forwarding 4242 → 4242 in the VM network settings):
```bash
ssh <login>@127.0.0.1 -p 4242
```

## Resources
 
### References
 
- [Debian Documentation](https://www.debian.org/doc/)
- [Debian Administrator's Handbook](https://debian-handbook.info/)
- [Debian Wiki: UFW](https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29)
- [Debian Wiki: AppArmor](https://wiki.debian.org/AppArmor)
- [Debian Wiki: LVM](https://wiki.debian.org/LVM)
- [Arch Wiki: dm-crypt / LUKS](https://wiki.archlinux.org/title/Dm-crypt)
- [sudoers manual](https://www.sudo.ws/docs/man/sudoers.man/)
- [OpenSSH manual (`sshd_config`)](https://man.openbsd.org/sshd_config)
- [`libpam-pwquality` manual](https://manpages.debian.org/pam_pwquality)
- [Red Hat: SELinux overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/using_selinux/index)
- [Rocky Linux Documentation](https://docs.rockylinux.org/)
- [firewalld documentation](https://firewalld.org/documentation/)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [UTM Documentation](https://docs.getutm.app/)
- `man cron`, `man crontab`, `man wall`, `man chage`, `man ufw`
