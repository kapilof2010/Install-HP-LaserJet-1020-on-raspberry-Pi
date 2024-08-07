# How to set up an HP LaserJet 1020 printer server using CUPS on Raspberry Pi OS Bullseye
## Set up the Raspberry Pi
Note: This probably also works in Raspberry Pi OS Bookworm, but I have only tested this on my Pi 1 B+ that doesn't support Bookworm
- Flash the Raspbian Bullseye Lite image to the SD card
- Configure the user account, network and SSH settings while imaging to make setup easier.
## Install the required packages
You need to install CUPS, HPLIP, and the foo2zjs printer drivers, use the following commands:
```
sudo apt update
sudo apt install cups hplip printer-driver-foo2zjs
```
CUPS will handle managing printers and sharing them to your network, HPLIP is required to upload the printer firmware to the printer, and the foo2zjs drivers are required for printing.
## Set up CUPS
Run the following commands to allow configuring CUPS without logging in as root and from other computers in the network:
```
sudo usermod -a -G lpadmin [your username]
sudo cupsctl --remote-any
sudo systemctl restart cups
```
## Set up the HP Plugin
The HP LaserJet 1020 requires its firmware to be uploaded everytime its connected, HPLIP handles this automatically after setting it up.
Plug in your printer, turn it on, and then run the following command:

```
sudo hp-plugin -i
```

Then follow these steps:
1. Choose "Download plug-in from HP (recommended)"
2. Accept the license terms for the plugin
3. Wait for it to install
## Configure your printer in CUPS
1. Open the web interface for CUPS by navigating to `https://[Your pi's IP]:631`(https://192.168.1.17:631 for example)
2. Click "Administration" in the top navigation bar
3. Click the "Add printer" button under Printers
4. Select "HP LaserJet 1020 USB FN2Z0GV HPLIP (HP LaserJet 1020)" under "Local Printers"
5. Check "Share this printer" to use it across the network.
6. Click "Continue"
7. Select "HP LaserJet 1020 Foomatic/f002zjs-z1 (recommended) (en)" under the printer model, make sure that the make is "HP"
8. Click "Add printer"
9. Click "Set default options"

Now you need to set the postscript renderer to ghostscript in cups-filters, run the following command in the terminal using your printer's name("HP_LaserJet_1020" for example):
```
lpadmin -p [your printer's name in CUPS] -o pdftops-renderer-default=gs
```
## Test the printer
Go back to the CUPS web interface, and navigate to your printer's page, click the "Maintenance" drop down and click "Print test page", you have successfully set up your printer if it prints without any problems.
