---
layout:   post
title:    Setup SVN Server di Ubuntu 10.04 (Lucid)
comments: true
summary:  Langkah-langkah bagaimana setup SVN Server di ubuntu 10.04
---

Pertama pastikan Apache sudah terinstall:
    
{% highlight bash %}
sudo apt-get install apache2
{% endhighlight %}

Kemudian install Subversion dan Subversion Apache module:
		
{% highlight bash %}
sudo apt-get install subversion libapache2-svn
{% endhighlight %}

Buat folder untuk menyimpan repositori:

{% highlight bash %}
sudo mkdir /var/svn
sudo mkdir /var/svn/repositories
{% endhighlight %}

Buat repositori svn. Sebagai contoh, kita buat repositori dengan nama 'test':

{% highlight bash %}
sudo svnadmin create /var/svn/repositories/test
{% endhighlight %}

Ubah permission folder `test` sehingga apache bisa membaca dan merubah file maupun folder dalam repositori

{% highlight bash %}
sudo chown	-R www-data:www-data /var/svn/repositories/test
{% endhighlight %}

Kemudian jika kita ingin membuat repositori hanya bisa diakses oleh user yang terdaftar dalam sistem, buat file password dengan cara:

{% highlight bash %}
sudo htpasswd -c /etc/subversion/passwd segi
{% endhighlight %}

Masukkan password untuk user `segi`. Hilangkan `-c` untuk memastikan user baru ditambahkan ke dalam file bukannya di replace.

Lalu ubah konfigurasi apache supaya repositori bisa diakses melalui web:

{% highlight bash %}
sudo gedit /etc/apache2/apache2.conf
{% endhighlight %}

Dan tambahkan kode berikut di akhir file:

{% highlight bash %}
<Location /svn>
  DAV svn
  SVNParentPath /var/svn/repositories/
  SVNListParentPath On
  AuthType Basic
  AuthName "Test"
  AuthUserFile /etc/subversion/passwd
  <LimitExcept GET PROPFIND OPTIONS REPORT>
    Require valid-user
  </LimitExcept>
</Location>
{% endhighlight %}

Simpan file konfigurasi Apache:

{% highlight bash %}
sudo /etc/init.d/apache2 restart
{% endhighlight %}

Sekarang repositori `test` bisa diakses melalui:

{% highlight bash %}
http://localhost/svn/test
{% endhighlight %}