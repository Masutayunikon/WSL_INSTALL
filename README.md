# My_own_Windows_installation_with_WSL (10 and 11)

## WSL

Install wsl -> https://docs.microsoft.com/fr-fr/windows/wsl/install \
Install docker -> https://docs.docker.com/desktop/windows/wsl/ \
Install fedora34 -> https://dev.to/bowmanjd/install-fedora-on-windows-subsystem-for-linux-wsl-4b26 \
The current distro i using -> https://koji.fedoraproject.org/koji/buildinfo?buildID=1920537 \
Install Epitech dump -> https://github.com/Epitech/dump \
// Maybe useless because he need a good sending packet in connection :/
Install Xserver for GUI in wsl -> https://stackoverflow.com/questions/61110603/how-to-set-up-working-x11-forwarding-on-wsl2 \
Install PulseAudioServer for wsl sound -> //TODO (Did not work when I tried)\

## WSL SSH SERVER

Install systemctl and fix error-> https://unix.stackexchange.com/questions/639866/how-to-start-a-service-in-a-wsl-based-fedora \
Install ssh with openssh and change config file port -> https://www.hanselman.com/blog/how-to-ssh-into-wsl2-on-windows-10-from-an-external-machine \
You can make bridge connection with windows \
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=OPENVPN_WSL_PORT connectaddress=WSL_IP connectport=WINDOWS_PORT \
netsh advfirewall firewall add rule name="NAME" dir=in action=allow protocol=TCP localport=WINDOWS_PORT \

Powershell script for auto find in internet

```powershell
# Start SSH Service.
wsl sudo service ssh start

# WSL2 network port forwarding script v1
#   for enable script, 'Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser' in Powershell,
#   for delete exist rules and ports use 'delete' as parameter, for show ports use 'list' as parameter.
#   written by Daehyuk Ahn, Aug-1-2020

# Display all portproxy information
If ($Args[0] -eq "list") {
    netsh interface portproxy show v4tov4;
    exit;
} 

# If elevation needed, start new process
If (-NOT ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator))
{
  # Relaunch as an elevated process:
  Start-Process powershell.exe "-File",('"{0}"' -f $MyInvocation.MyCommand.Path),"$Args runas" -Verb RunAs
  exit
}

# You should modify '$Ports' for your applications 
$Ports = (2222,80,443,8080)

# Check WSL ip address
wsl hostname -I | Set-Variable -Name "WSL"
$found = $WSL -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';
if (-not $found) {
  echo "WSL2 cannot be found. Terminate script.";
  exit;
}

# Remove and Create NetFireWallRule
Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock';
if ($Args[0] -ne "delete") {
  New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $Ports -Action Allow -Protocol TCP;
  New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $Ports -Action Allow -Protocol TCP;
}

# Add each port into portproxy
$Addr = "0.0.0.0"
Foreach ($Port in $Ports) {
    iex "netsh interface portproxy delete v4tov4 listenaddress=$Addr listenport=$Port | Out-Null";
    if ($Args[0] -ne "delete") {
        iex "netsh interface portproxy add v4tov4 listenaddress=$Addr listenport=$Port connectaddress=$WSL connectport=$Port | Out-Null";
    }
}

# Display all portproxy information
netsh interface portproxy show v4tov4;

# Give user to chance to see above list when relaunched start
If ($Args[0] -eq "runas" -Or $Args[1] -eq "runas") {
  Write-Host -NoNewLine 'Press any key to close! ';
  $null = $Host.UI.RawUI.ReadKey('NoEcho,IncludeKeyDown');
}
```

endless point fix

```
Please review response post by @PhyX-Meow re: yuk7/ArchWSL#248.
As he points out, it really has nothing to do with your Linux install. It's a simple fix in Windows.
The solution he posts is for Arch, but the fix is exactly same for Ubuntu, etc. in a WSL2 install.
The issue is that Windows delivers libcuda.so, libcuda.so.1, and libcuda.so.1.1 as fully separate copies of the same file.
The fix is just to remove libcuda.so and libcuda.so.1, and just make sym links for each of them to libcuda.so.1.1

Run a command line shell as Administrator, type "cmd" to get a non-powershell command line.

Then type the following commands to create the problematic symbolic links:

C:
cd \Windows\System32\lxss\lib
del libcuda.so
del libcuda.so.1
mklink libcuda.so libcuda.so.1.1
mklink libcuda.so.1 libcuda.so.1.1

when you're done, it will look like this:

C:\Windows\System32\lxss\lib> DIR
... ...
Directory of C:\Windows\System32\lxss\lib
03/15/2022 04:00 PM

.
03/15/2022 03:59 PM libcuda.so [libcuda.so.1.1]
03/15/2022 04:00 PM libcuda.so.1 [libcuda.so.1.1]
Then, just finish your command you were running,
in my case, the solution was just run "apt reinstall libc-bin". This is because libc-bin was getting the errors when I had run "apt upgrade -y" command. (see below)

The error I received in my "apt upgrade -y" command was two lines:
#> apt upgrade -y
.... < stuff deleted > ...
Processing triggers for libc-bin (2.31-0ubuntu9.7) ...
/sbin/ldconfig.real: /usr/lib/wsl/lib/libcuda.so.1 is not a symbolic link
... < stuff deleted > ...

As per @PhyX-Meow

"Actually this is not relate to Arch, nor ArchWSL. It's caused by libcuda.so in your C:\Windows\System32\lxss\lib\ folder not a symbolic link, which is installed by nvidia driver. One solution to [remove] the warning is delete libcuda.so and libcuda.so.1 and use make symbolic link to libcuda.so.1.1. Command line: mklink . Note the command not work in powershell, you shall use cmd.exe."

:)
```

https://github.com/microsoft/WSL/issues/5548#issuecomment-912495487 \
https://github.com/microsoft/WSL/issues/5548#issuecomment-990521993

## WSL custom

install ohmyposh -> https://ohmyposh.dev/ \
install nerdfont -> https://www.nerdfonts.com/ \
install boxes -> <code>yum install boxes</code> \
install lolcat -> <code>dnf install lolcat</code> \
my .bashrc:
```bash
eval "$(oh-my-posh prompt init bash --config ~/.poshthemes/cert.omp.json)"
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0

boxes -a c -d dog <<< "Hello $(whoami),today is $(date)" | lolcat
```

## Windows custom

Set the taskbar transparent -> Download TranslucentTB in windows store \
:warning: **Sometimes glitched but is the best solution i found for last versions of windows**: \
set the taskbar disable for mouse activation -> PushToDown file in my repository (PushToDown.exe)

## Clion

set the template header -> https://www.jetbrains.com/help/idea/using-file-and-code-templates.html
```c
/*
** EPITECH PROJECT, ${YEAR}
** ${FILE_NAME}
** File description:
** ${PROJECT_NAME} utils function
*/
```
  
add this part for header files
  
```c
#[[#ifndef]]# ${NAME}
#[[#define]]# ${NAME}

#[[#endif]]# //${NAME}
```

change newline settings for linux new line if you need \
