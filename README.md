novita
=====

Description (in Bahasa)
-----------------------

Sistem Informasi Tugas Akhir

novita adalah sebuah sistem informasi untuk mendata tugas akhir mahasiswa. Dibuat menggunakan JSP.
Dibuat pertama kali oleh Mahasiswa Teknik Informatika Politeknik Kampar, Andri Noviar AM, A.Md

Hak Cipta &copy; 2013 Program Studi Teknik Informatika Politeknik Kampar.


INSTALL
-------

novita berjalan sebagai servlet, dan Anda harus menginstal Tomcat. 
### Instaling VirtualHost di tomcat

#### Set VirtualHost di Tomcat6
Tambahkan host berikut, ganti novita.poltek-kampar.ac.id sesuai dengan settingan DNS Anda.
```
      <Host name="novita.poltek-kampar.ac.id"  appBase="novita"
            unpackWARs="true" autoDeploy="true"
            xmlValidation="false" xmlNamespaceAware="false">
      </Host>
```

di ubuntu, berkas server.xml ada di
/etc/tomcat6/server.xml

#### Buat User Baru, novita
```
sudo adduser novita
mkdir /home/novita/public_html
```

#### Point appBase "novita" seperti yang ada di server.xml
```
sudo mkdir /var/lib/tomcat6/novita
ln -s /home/novita/public_html ROOT
```
sehingga nantinya ROOT akan mempoint ke /home/novita/public_html
```
root@tomcat6svr:/var/lib/tomcat6/novita# dir
total 5464
drwxr-xr-x 2 root  root     4096 2013-11-07 12:05 .
drwxr-xr-x 7 root  root     4096 2013-10-29 15:16 ..
lrwxrwxrwx 1 root  root       24 2013-10-29 15:35 ROOT -> /home/novita/public_html/
```

#### download berkas master.zip disini, dan extract ke /home/novita/public_htmll/
download berkas disini, dan point ke /home/novita/public_html/
jangan sampai ada folder master atau nama branch lainnya di sana, tapi langsung ke folder yang ada README.md ini.

#### restart tomcat6
restart tomcat6
```
sudo /etc/init.d/tomcat6 restart
```

#### create MySQL database
Create User novita dengan password novita
```
CREATE USER 'novita'@'localhost' IDENTIFIED BY  'novita';
GRANT USAGE ON * . * TO  'novita'@'localhost' IDENTIFIED BY  'novita' WITH MAX_QUERIES_PER_HOUR 0 MAX_CONNECTIONS_PER_HOUR 0 MAX_UPDATES_PER_HOUR 0 MAX_USER_CONNECTIONS 0 ;
```

Buat database novita, dan izinkan user novita untuk mengakses database ini.
```
CREATE DATABASE IF NOT EXISTS  `novita` ;
GRANT ALL PRIVILEGES ON  `novita` . * TO  'novita'@'localhost';
```

terkadang mesti di FLUSH PRIVILEGES di mysqlnya.

Perhatikan bahwa perintah SQL di atas untuk membuat user novita dengan password novita, jika Anda ingin mengubah settingan, Anda mesti mengubah source code
```
/home/novita/public_html/admin/koneksi.jsp
```

#### Populate database dengan berkas *.sql yang diberikan
kalau ada phpMyAdmin terkadang lebih gampang. kalau tidak, Anda dapat melakukannya di konsol dengan perintah
```
mysql -unovita -pnovita novita < novita-2014-02-03.sql
```
arti perintah di atas adalah: mysql dengan user novita, password novita database novita, lalu panggil semua perintah di berkas novita-2014-02-03.sql ke database tadi.


### Optional: AJP Proxy
Jika Apache telah berjalan di port 80, dan tomcat di port 8080, maka Anda dapat melakukan proxy sehingga apache yang di port 80 dapat mengakses novita tanpa harus menggunakan port 8080.

Contoh tanpa proxy:
http://novita.poltek-kampar.ac.id:8080/

Contoh dengan proxy:
http://novita.poltek-kampar.ac.id/

Caranya adalah dengan membuat sebuah berkas di /etc/apache2/sites-available
```
sudo pico /etc/apache2/sites-available/novita.poltek-kampar.ac.id
```
Kemudian isikan settingan VirtualHost Apache berikut:
```
<VirtualHost _default_:80>
  ServerName novita.poltek-kampar.ac.id
  ServerAlias www.novita.poltek-kampar.ac.id
  UseCanonicalName Off
  VirtualDocumentRoot /home/novita/public_html
  <Directory "/home/novita/public_html">
    allow from all
    Options +Indexes +FollowSymLinks
  </Directory>
        <Proxy *>
                Order deny,allow
                allow from all
        </Proxy>
        ProxyRequests Off
        ProxyPass / ajp://localhost:8009/ smax=0 ttl=60 retry=5
</VirtualHost>
```
Pastikan Tomcat melakukan layanan AJP di port 8009 (default)
check di settingan server.xml tomcat
```
    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />

```

Enable VirtualHost
```
sudo a2ensite novita.poltek-kampar.ac.id
```
Check apakah sites sudah enabled:
```
cat /etc/apache2/sites-enabled/novita.poltek-kampar.ac.id
```
Reload Apache
```
sudo /etc/init.d/apache2 reload
```

Pada contoh di atas, berkas novita ada di /home/novita/public_html, sehingga user
