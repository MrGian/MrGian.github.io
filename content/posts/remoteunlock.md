+++
title = "Full disk encryption with SSH remote unlocking on Arch Linux"
date = "2022-07-29"
+++

I'm using LUKS **full disk encryption** on my desktop, it is a very convenient and secure way to protect my data.\
It makes practically **impossible** to access your data without knowing the decryption key. Just insert your passphrase at boot and you are good to go.

But what about encryption on an [home server](/posts/homeserver)?\
It is **not convenient at all** to phisically insert a passphrase everytime you reboot your server. What if you haven't physical access to your server and you need to unlock it because of a sudden reboot?

The solution is **ssh remote unlocking**.

## SSH remote unlocking

It consists in setting up a small SSH server, that **is able to run before the OS starts**, only for the purpose of letting you insert the passphrase remotely.\
How to setup full disk encryption is out of the scope of this tutorial, so I'm assuming you've already done that.

Let's start!

### Needed packages

First we need to install some packages: 
- *mkinitcpio-netconf* to be able to assign a static IP at boot time
- *mkinitcpio-tinyssh* provides the ssh server
- *mkinitcpio-utils* provides the shell

Install them using this command:\
```sudo pacman -S mkinitcpio-netconf mkinitcpio-tinyssh mkinitcpio-utils```

### Generating the keys

For better security, we will use key-based SSH authentication, so we need to generate a key pair on the **client machine**. Since Tiny SSH only supports Ed25519 key pairs we generate them with:\
```ssh-keygen -t ed25519```\
(Remember to leave the passphrase empty)

You will find your key pair in ```.ssh``` in your home directory:
- ```/home/yourname/.ssh/id_ed25519.pub``` is the public key
- ```/home/yourname/.ssh/id_ed25519``` is the private key

Move the key pair to the **server** and then move the **public key** in the Tiny SSH config folder:\
```sudo mv id_ed25519.pub /etc/tinyssh/root_key```

### Adding the keys

We need to convert our private key to a format Tiny SSH is able to read.\
First remove the ssh key dir directory if exists: ```sudo rm -rf /etc/tinyssh/sshkeydir/```\
and then convert the private key with ```sudo tinyssh-convert /etc/tinyssh/sshkeydir/ < id_ed25519```

### Hooks

Then we need to add the necessary **hooks** to the mkinitcpio config file:\
```sudo nano /etc/mkinitcpio.conf```

Add ```netconf tinyssh encryptssh``` **before** ```filesystems```, it should look like this: 

```
MODULES=()
BINARIES=()
FILES=()
HOOKS=(base udev autodetect keyboard keymap modconf block netconf tinyssh encryptssh filesystems fsck)
```

Then regenerate the initramfs with: ```mkinitcpio -P```

### Configuring the kernel parameters

I'm using **systemd-boot** as bootloader, but if you are using GRUB it's the same, just edit the appropriate config file.

We need to configure two **kernel** parameters:
- ```cryptdevice=``` to indicate which device to unlock
- ```ip=``` to specify a static local IP at boot time, for being able to connect to the system before the OS starts

My bootloader entry ```/boot/loader/entries/arch.conf``` looks like this:

```
title Arch Linux
linux /vmlinuz-linux
initrd /intel-ucode.img
initrd /initramfs-linux.img
options cryptdevice=PARTUUID=<uuid>:luksdev:allow-discards root=/dev/mapper/luksdev ip=dhcp zswap.enabled=0 rw rootfstype=ext4
```

Notice the ```ip=dhcp``` parameter.

### Connection

Reboot you server and try to SSH to it using the root user.\
For example ```ssh root@yourserver.com```, you should get a prompt like this: ```Enter passphrase for /dev/sda2: ```\
enter your passphrase and press enter, the connection should automatically close when it's done.

### Conclusion

And that's it! If you have any problem just let me know.
You can contact me using the links in the home page.

See you soon!