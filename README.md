# My_own_Windows_installation_with_WSL (10 and 11)

## WSL

Install wsl -> https://docs.microsoft.com/fr-fr/windows/wsl/install \
Install docker -> https://docs.docker.com/desktop/windows/wsl/ \
Install fedora34 -> https://dev.to/bowmanjd/install-fedora-on-windows-subsystem-for-linux-wsl-4b26 with this distro -> https://koji.fedoraproject.org/koji/buildinfo?buildID=1920537 \
Install Xserver for GUI in wsl -> https://stackoverflow.com/questions/61110603/how-to-set-up-working-x11-forwarding-on-wsl2 \
Install PulseAudioServer for wsl sound -> //TODO (Did not work when I tried) \

## WSL custom

install ohmyposh -> https://ohmyposh.dev/ \
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
set the taskbar disable for mouse activation -> PushToDown file in my repository (PushToDown.exe) \

## Clion

set the template header -> https://www.jetbrains.com/help/idea/using-file-and-code-templates.html \
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
