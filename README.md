# OptiMac
My hackintosh stuff for Dell Optiplex 7020/9020

This is not a complete guide, but those who have made hackintoshes before this could be handy. Though for most the EFI folder itself should be enough to get going. I also suck at writing these kind of things. But I do like sharing.

Please only use this for clean installs, or updating an existing OpenCore install. I replaced my Clover at first and the system wasn't as fast as when I tried a clean install to test my EFI folder before using it on other 7020/9020 boxes. The difference was quite noticeable. So only do a clean install if you're coming from Clover and just import your user data/apps once installed. This will ensure maximum performance.

Also don't forget to reset NVRAM from the OpenCore menu the first time, and if you boot with Clover again reset it again before booting with OpenCore. This will prevent any weird issues. My BIOS settings are simple: load factory defaults and enable legacy rom loading.

In order to use this you ***must*** be on the A18 BIOS, disable *CFG Lock* (see below) and change the following fields in config.plist;

PlatformInfo -> Generic -> MLB

PlatformInfo -> Generic -> ROM

PlatformInfo -> Generic -> SystemSerialNumber

PlatformInfo -> Generic -> SystemUUID

You can generate the MLB/Serial/UUID with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). Put your ethernet mac address in the ROM field without semicolons. For more information please refer to [this](https://khronokernel.github.io/Opencore-Vanilla-Desktop-Guide/config.plist/haswell.html) guide.

Optionally but highly recommended is to change the *PlatformInfo -> Generic -> SystemProductName* field as well. Find one that matches your CPU as close as possible. In my case that was 14,3. When in doubt use 14,1 with only iGPU, 14,2 when used with dGPU and 15,1 for Haswell Refresh.

Please use [ProperTree](https://github.com/corpnewt/ProperTree) to edit the OpenCore config.

To disable CFG Lock you can either use a quirk in OpenCore or disable it properly. Luckily we can can disable it. Refer to [this](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/msr-lock) guide on how to get everything ready. The magic number for the A18 BIOS is: 0xDA2. Executing *"setup_var 0xDA2 0x0"* will disable CFG Lock. Revert it simply execute the command again but replace 0x0 with 0x1.

While we're here we can also optimise the Intel HD4x00 Framebuffer by setting the DVMT pre-alloc to 64MB, which macOS likes. This is not really needed but recommended. Execute *"setup_var 0x263 0x2"* to change it. By default it's set to 0x1 which is 32MB.

Note: Resetting NVRAM or loading BIOS defautls does ***not*** clear these changes. The motherboard jumper may clear them, I have yet to test that. Double check you're entering the right values and nothing could go wrong really.

Credit for above goes to @JimLee1996 and his nice [write up](https://github.com/JimLee1996/Hackintosh_OptiPlex_9020) on this subject.

If you haven't already, [download](https://github.com/corpnewt/gibMacOS) and [create the install media](https://support.apple.com/sl-si/HT201372). If you don't have a working macOS system yet you can still create an installer by running gibMacOS's Makeinstall.bat as administrator on Windows.

Download [EFI Agent](https://github.com/headkaze/EFI-Agent/releases) and use it to easily mount the EFI partition of the installer and copy the EFI found in this repository to it. You're now ready to install macOS. Once installed mount the EFI partition of your internal disk and once again copy the EFI folder found here to it. 

Before rebooting from the internal disk there are a few more very important steps, one is to generate a SSDT file that matches your CPU. To do so run the followling command:
*"curl -o ./ssdtPRGen.sh https://raw.githubusercontent.com/Piker-Alpha/ssdtPRGen.sh/Beta/ssdtPRGen.sh && chmod +x ./ssdtPRGen.sh && ./ssdtPRGen.sh"*

Choose *no* when asked to save the dsl file unless you want it. With the EFI folder on the internal disk still mounted copy the new SSDT and replace the old one by running: *"cp ~/Library/ssdtPRGen/ssdt.aml /Volumes/EFI/OC/ACPI/SSDT.aml"*. Once copied it is safe to remove the crud left behind by the script. Issue "*rm -rf ~/Library/ssdtPRGen"* to get rid of it.

Another important step is to enable TRIM support. Close all open apps, open a terminal and execute *"sudo trimforce enable"*. Enter yes for both questions and once rebooted TRIM should be enabled.

Please also download [Hackintool](https://github.com/headkaze/Hackintool/releases) as you will need to fix sleep image, verify settings and lots of other stuff and also easily find updates for kexts and OpenCore itself.

Reboot and you should be running macOS on your ~~OptiPlex~~ OptiMac!

Have fun!

Tip: To make ProperTree into a little app, double click on the *buildapp.command* file inside the script folder. The resulting app will be put in the main ProperTree folder. 

PS: I'll be updating this file more often than is healthy so please ignore all the silly commits, lol.

Post install downloads:
https://github.com/pqrs-org/Karabiner-Elements/releases
https://titanium-software.fr/en/onyx.html
https://software.intel.com/en-us/articles/intel-power-gadget/

TODO:
- Add disabling of MEBx and also howto add KVM to MEBx. It's cool to have.
- Cleaner USB config, ideally not using FakePCIID but for now it works flawlessy with FakePCIID.
- FileVault2 testing, the config is ready for it.
- Enable TRIM with a patch in the config cuz I always forget to run *trimforce*.
- Wifi, I haven't received my Broadcom wifi/BT combo card yet.
- Bluetooth, currently using a [$2 BT 4.0 dongle](https://www.ebay.co.uk/itm/1PCS-Mini-USB-Bluetooth-V4-0-3Mbps-20M-Dongle-Dual-Mode-Wireless-Adapter-Device/324106977844) that surprisingly works out of the box. No handoff or other fancy features are supported but audio and mouse/keyboard work fine.
