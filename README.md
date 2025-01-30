
## HTB Host Reader
A small script that retrieves the first line below #HTB in `/etc/hosts`. 
If the line contains an IP and hostname, it displays the information in the panel and allows copying the IP to the clipboard with a click.

**Dependencies:**  
xclip, install `sudo apt install xclip`

**Instructions:**  
This script works on Kali Linux and requires `xfce4-genmon`.  
1. Save the script anywhere you like.  
2. Make it executable (`chmod +x script.sh`).  
3. Add a Genmon widget in XFCE.  
4. Set the script’s path in the widget’s command field.  
5. Choose a font, size, and refresh interval.  

Now it will display the IP from `/etc/hosts` (if available) and allow copying it with a click.  

Add IP and hostname
```bash
echo -e "#HTB\n10.10.1337.10 machine.htb www.machine.htb" | sudo tee -a /etc/hosts > /dev/null
```
In /etc/hosts
```bash
#HTB
10.10.1337.10 machine.htb www.machine.htb
```

xfce-genmon-ctfhost.sh
```bash
#!/bin/sh

# Find the line below #HTB in the /etc/hosts file
htb_entry=$(awk '/#HTB/{getline; print}' /etc/hosts)

if [ -n "$htb_entry" ]; then
  # Extract only the IP (assuming format 'IP name')
  ip=$(echo "$htb_entry" | awk '{print $1}')
  
  # Display icon and full entry
  printf "<icon>computer-fail-symbolic</icon>"
  printf "<txt>%s</txt>" "$htb_entry"

  # Copy only the IP to clipboard on click
  if command -v xclip > /dev/null 2>&1; then
    printf "<iconclick>sh -c 'echo %s | xclip -selection clipboard'</iconclick>" "$ip"
    printf "<txtclick>sh -c 'echo %s | xclip -selection clipboard'</txtclick>" "$ip"
    printf "<tool>IP (click to copy)</tool>"
  else
    printf "<tool>Install xclip to copy the IP</tool>"
  fi
else
  # If there's no data below #HTB
  printf "<txt></txt>"
fi

```
