---
layout:   post
title:    Membuat Modem Huawei 3531 Bekerja di Ubuntu 14.04
comments: true
summary:  Modem Huawei 3531 tidak terbaca di ubuntu 14.04? Ini solusinya.
---

Salah satu kesulitan (tantangan!) yang biasa dihadapi pengguna linux adalah modem tertentu sering kali tidak bisa langsung digunakan. Salah satunya adalah modem Huawei 3531. Beriku ini adalah salah satu cara untuk mengkonfigurasi modem Huawei 3531 sehingga bisa digunakan pada Ubuntu 14.04.

#### Install USB Modeswitch

Sebelum menginstall usb-modeswitch pastikan system sudah terinstall `libusb`, jika belum install `libusb` dengan `sudo apt-get install libusb1-dev`.

Download usb-modeswitch versi terbaru (2.2.1):

1. [usb-modeswitch](http://www.draisberghof.de/usb_modeswitch/usb-modeswitch-2.2.1.tar.bz2)
2. [usb-modeswitch-data](http://www.draisberghof.de/usb_modeswitch/usb-modeswitch-data-20150115.tar.bz2)

Install usb-modeswitch:

1. Extract file usb-modeswitch-2.2.1.tar.bz2
2. Jalankan perintah `sudo make` di dalam folder usb-modeswitch-2.2.1

Install usb-modeswitch-data:

1. Extract file usb-modeswitch-data-20150115.tar.bz2
2. Jalankan perintah `sudo make` di dalam folder usb-modeswitch-2.2.1

#### Install Supervisor

Install supervisor dengan menjalankan perintah `sudo apt-get install supervisor`.

Tulis script di `/usr/bin/broadband` dan buat executable

{% highlight bash %}
#!/bin/bash
# Script to enable Mobile Broadband
# Author: (hanynowsky@gmail.com)
WAIT=25
# Huawei E3531s-2
localcon="Meditel2"
connector(){
isBUP=$(nmcli con status | grep -i "${localcon}")
if [ -z "${isBUP}" ];then
echo "Broadband Connection Status is void: ${isBUP}"
nmcli nm wwan on;
sleep 1;
nmcli nm wwan off;
sleep 1;
if [ -n "${isBUP}" ];then
echo "Switching Off connection to ${localcon}"
nmcli con down id "${localcon}";
fi
sleep 1;
echo "Trying to Connect to $localcon"
nmcli con up id "${localcon}";
if [ "$?" -eq 0  ]; then
echo "Connection Established."
fi
sleep 1
fi
#exit 0;
}
 
while true; do
if [ -n "$(lsusb | grep '12d1:15ce')" ]; then
# This will switch to modem mode : 12d1:15b1
sudo usb_modeswitch -v 12d1 -p 15ce -M '55534243123456780000000000000011062000000100000000000000000000'
echo "Activating modem, please wait ${WAIT} seconds."
notify-send -u normal -a broadband "Connecting Broadband" "Modem $(lsusb | grep -i huawei). \nPlease wait 25 seconds"
sleep $WAIT
fi
isModem=$(lsusb | grep -iE '12d1:15b1|modem on')
if [ -n "${isModem}" ]; then
eval connector
fi
done
{% endhighlight %}

Ganti `localcon="Meditel2"` dengan nama Broadband Connection yang telah anda buat sebelumnya.

Ganti `12d1` dan `15ce` dengan kode product dan vendor modem anda. Jalankan `lsusb` untuk melihat kode product:vendor dari usb devise yang terkoneksi.

Restart supervisor `sudo service supervisor restart`

Berikutnya modem di masukkan, modem akan dapat dikoneksikan melalui Broadband connection yang sebelumnya sudah dibuat.


Sumber: [hanynowsky.wordpress.com](https://hanynowsky.wordpress.com/2015/03/01/huawei-e3531s-2-on-ubuntu-14-04/)