---
layout: page
title: Using VSCode over SSH
parent: Tips
---

# Using VSCode over SSH

## Intro
Visual Studio code has a great feature which allows you to use your Windows PC to remotely edit, compile and debug code on your embedded linux platform. Visual Studio Code runs on the PC with a remote-ssh extension installed, and communicates with the platform via SSH. The SSH server component of VSCode is automatically installed on the platform when you first connect.

## Steps
1. Ensure you have Visual Studo Code installed on the PC.
2. Install the Remote-SSH extension.
3. Set up credentials
    1. Run `ssh-keygen.exe` in the Windows PC terminal to generate a key. If you use the default options you should end up with two files under `~\.ssh`. `ia_rsa` is the identity file with the private key and `is_rsa.pub` contains the public key to be installed on the remote platform.
    2. On the remote platform, create file `~/.ssh/authorized_keys`. Paste the contents of `is_rsa.pub` that was generated in the previous step into the `authorized_keys` file and save.
    3. On the Windows PC, open the SSH config file in `~\.ssh\config`. Add details of the platform. Here's an example;
    ```
    Host rpi
        HostName 192.168.1.117
        User pi
        IdentityFile ~/.ssh/id_rsa
    ```
    The config file is also accessible through VSCode in the Remote Explorer panel by clicking the 'configuration' icon.
    4. To verify that the keys have been correctly installed, open an ssh session from the windows PC terminal: `ssh rpi`. You should be able to connect without entering a password. You can close the SSH session using `logout`.
4. Configure remote platform setting
    1. In VSCode press `ctrl+,` to open settings.
    2. Navigate to the section `Remote.SSH: Remote Platform`. You can paste this into the search bar.
    3. Click 'Add item' and add an entry for your platform. In my case the item is `rpi` and the value is `linux`.
5. Connect to the SSH target 
    1. Opening the Remote Explorer panel on the left, and select the target that you just configured (`rpi` in this example). Click 'Connect to host in new window'.
    2. Wait while connection is established and the VSCode server is automatically installed on the platform.
6. You should now be able to open files & folders on the remote platform from your windows VS Code instance! Try `File > Open File` or `File > Open Folder`.
7. To disconnect, hit `F1` and type `Remote: Close Remote Connection`. You can also access this by clicking the green icon in the bottom left with the angle brackets.