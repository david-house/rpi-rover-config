# Install Influxdb

## Install the repo keys
```bash
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
```

## Determine the version of Raspian
```bash
lsb_release -a
```

## Update the sources (for stretch)
```bash
sudo apt install apt-transport-https
echo "deb https://repos.influxdata.com/debian stretch stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
sudo apt update
```

## Install https transport for RPi if not already installed
```bash
sudo apt install apt-transport-https
```

## Install influxdb package
```bash
sudo apt-get install influxdb
```

## Edit the Influxdb configuration file as needed
```bash
sudo nano -c /etc/influxdb/influxdb.conf
```

## Find the mount reference for any removable storage
```bash
systemctl list-units | grep '/path/to/mount' | awk '{ print $1 }'
```
This should return a reference like *.mount to your drive. If your mount point was `/mnt/usbdrive` then it may be something like `mnt-usbdrive.mount`

## Change the Influxdb service to start after removeable storage is mounted
```bash
sudo nano /etc/systemd/system/influxd.service
```
Under the `[Unit]` configuration section, in the `After=` clause add the .mount file after a space between items

## Start the influx service
```bash
sudo systemctl unmask influxdb.service
sudo systemctl start influxdb
```
## Set the data storage folder in the influxdb.conf
Create the data storage folders if needed, and set the ownership
```bash
sudo mkdir /mnt/usbdrive/influxdb
sudo mkdir /mnt/usbdrive/influxdb/data
sudo mkdir /mnt/usbdrive/influxdb/wal

sudo chown influxdb:influxdb -R /mnt/usbdrive/influxdb
sudo chown influxdb:influxdb -R /mnt/usbdrive/influxdb/data
sudo chown influxdb:influxdb -R /mnt/usbdrive/influxdb/wal
```

Edit the file `sudo nano /etc/influxdb/influxdb.conf`
In the `[data]` section change the folder locations change folders as needed:
```
[data]
  # The directory where the TSM storage engine stores TSM files.
  dir = "/mnt/usbdrive/influxdb/data"

  # The directory where the TSM storage engine stores WAL files.
  wal-dir = "/mnt/usbdrive/influxdb/wal"
```

## Set the UDP port in the config
Edit the config file `/etc/influxdb/influxdb.conf`
Uncomment the UDP section and configure as desired:
```
[[udp]]
  enabled = true
  bind-address = ":8089"
  database = "sensor"
```







