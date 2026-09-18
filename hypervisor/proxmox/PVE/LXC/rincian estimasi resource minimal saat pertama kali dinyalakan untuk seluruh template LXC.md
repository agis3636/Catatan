Berikut adalah rincian estimasi *resource* minimal (RAM, CPU, dan Disk) saat pertama kali dinyalakan (kondisi *idle*) untuk seluruh template LXC yang ada.

Perlu diluruskan secara teknis: Template LXC di Proxmox (seperti TurnKey di gambar) itu kosong/belum punya alokasi resource saat didownload. Template itu cuma cetakan (file zip/tar.gz berisi OS dasar + aplikasi siap pakai). Resource baru akan "keluar" atau ditentukan saat Anda mengklik tombol Create CT dan mengisi spesifikasinya.

estimasi resource minimal riil yang "keluar" (dipakai) oleh masing-masing aplikasi TurnKey di daftar tersebut saat pertama kali dinyalakan dalam kondisi idle (standar best practice deployment):

---

### 1. Section: Mail

* **proxmox-mail-gateway-9.1-standard / 8.2-1**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 10 GB – 20 GB (Karena menjalankan filter spam, database mail, dan antrean mail gateway yang cukup aktif).

---

### 2. Section: System (OS Standar / Base Image)

*Template OS dasar ini sangat ringan karena tidak membawa aplikasi bawaan yang berat.*

* **alpine-3.22 / 3.23 / 3.24-default**: RAM 64 MB – 128 MB | CPU 1 Core | Disk 500 MB – 1 GB
* **archlinux-base**: RAM 256 MB | CPU 1 Core | Disk 2 GB
* **almalinux-9 / 10-default**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB – 3 GB
* **centos-9 / 10-stream-default**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **debian-12 / 13-standard**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB
* **devuan-5.0-standard**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB
* **fedora-43 / 44-default**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **gentoo-current-openrc**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **openeuler-25.03-default**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **opensuse-15.6 / 16.0-default**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **rockylinux-9 / 10-default**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB – 3 GB
* **ubuntu-22.04 / 24.04 / 25.04 / 26.04-standard**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB – 3 GB

---

### 3. Section: TurnKey Linux (Aplikasi Siap Pakai)

#### Kategori: Development, Tools & Automation

* **turnkey-ansible**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-codeigniter**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB – 4 GB
* **turnkey-django**: RAM 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-gitea**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB (Bergantung pada jumlah repo git)
* **turnkey-gitlab**: RAM 2 GB – 4 GB | CPU 2 Core | Disk 10 GB – 20 GB (GitLab terkenal memakan RAM besar karena komponen Ruby/Rails-nya)
* **turnkey-jenkins**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB (Berbasis Java, butuh resource agak lega)
* **turnkey-laravel**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB – 5 GB
* **turnkey-nodejs**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB – 3 GB
* **turnkey-processmaker**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 5 GB – 10 GB (Workflow engine berbasis PHP/MySQL yang cukup kompleks)
* **turnkey-rails (Ruby on Rails)**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-tkldev**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB (Development appliance builder)
* **turnkey-trac / turnkey-redmine**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB – 5 GB (Project management & issue tracking)
* **turnkey-tracks**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (GTD productivity tool)

#### Kategori: CMS & Website (WordPress, Drupal, Joomla, dll)

* **turnkey-avideo**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB+ (Platform video sharing, butuh resource untuk proses video)
* **turnkey-b2evolution**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-cakephp**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-concrete-cms / concrete5**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB – 5 GB
* **turnkey-drupal7 / drupal8 / drupal9 / drupal10**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB – 5 GB
* **turnkey-e107**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-ezplatform**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 5 GB – 10 GB (Enterprise CMS berbasis Symfony)
* **turnkey-foswiki**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-gallery**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 5 GB (Tergantung jumlah foto)
* **turnkey-ghost**: RAM 512 MB | CPU 1 Core | Disk 3 GB (Node.js blogging platform yang cukup ringan)
* **turnkey-gnusocial**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-ibexa**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 5 GB – 10 GB
* **turnkey-joomla3 / joomla4**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB – 5 GB
* **turnkey-leantime**: RAM 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-lapp / turnkey-lamp**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB (Stack dasar web server kosong)
* **turnkey-lighttpd-php-fastcgi**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (Sangat ringan dibanding Apache)
* **turnkey-magento**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB (E-commerce berat yang boros RAM & database)
* **turnkey-mahara**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-mibew**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (Live support chat)
* **turnkey-moinmoin**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (Python wiki)
* **turnkey-omeka**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-opencart**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB (E-commerce menengah)
* **turnkey-oscommerce**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-phpbb**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB (Forum klasik)
* **turnkey-phplist**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB (Newsletter manager)
* **turnkey-plone**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB (Python CMS)
* **turnkey-prestashop**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 5 GB – 10 GB (E-commerce populer)
* **turnkey-processwire**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-silverstripe**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-simplemachines**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-symfony**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-typo3**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 5 GB (Enterprise CMS, agak berat)
* **turnkey-ushahidi**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-vanilla**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB (Modern forum)
* **turnkey-web2py**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB
* **turnkey-wordpress**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB – 5 GB
* **turnkey-xoops**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB

#### Kategori: E-Learning, Collaboration & Knowledge Management

* **turnkey-bookstack**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-canvas**: RAM 2 GB – 4 GB | CPU 2 Core | Disk 10 GB – 20 GB (LMS berat berbasis Ruby/Rails/Node.js)
* **turnkey-collabtive**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-dokuwiki**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (Wiki berbasis teks, sangat ringan)
* **turnkey-etherpad**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 2 GB
* **turnkey-mattermost**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 5 GB – 10 GB (Alternatif Slack, butuh resource menengah)
* **turnkey-mayan-edms**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB (Document management system yang cukup intensif di CPU/RAM)
* **turnkey-mediawiki**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 3 GB
* **turnkey-moodle**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB (LMS populer yang butuh RAM lebih jika user banyak)
* **turnkey-odoo**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB (ERP & Business apps, cukup berat di database dan Python process)
* **turnkey-roundup**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB

#### Kategori: Storage, Cloud, Database & Infrastruktur Jaringan

* **turnkey-couchdb**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-domain-controller**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB (Samba Domain Controller)
* **turnkey-fileserver**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 10 GB+ (Tergantung kapasitas file yang disimpan)
* **turnkey-gameserver**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 5 GB – 10 GB (Sangat bergantung pada jenis game yang dihosting)
* **turnkey-lapp (PostgreSQL stack)**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB
* **turnkey-matomo**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB – 10 GB (Web analytics, database akan cepat membengkak jika traffic besar)
* **turnkey-mediaserver**: RAM 1 GB – 2 GB | CPU 2 Core | Disk 10 GB+ (Streaming media, butuh CPU untuk transcode jika diperlukan)
* **turnkey-mongodb**: RAM 1 GB – 2 GB | CPU 1 Core | Disk 5 GB (NoSQL database yang menyukai RAM besar)
* **turnkey-mysql**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB (Database murni)
* **turnkey-openldap**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB
* **turnkey-openvpn**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (Sangat ringan)
* **turnkey-otrs / turnkey-faveo-helpdesk**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 5 GB – 10 GB (Sistem tiket support)
* **turnkey-postgresql**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-redis**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 2 GB (In-memory cache, sangat bergantung pada RAM)
* **turnkey-sahana-eden**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-snipe-it**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB (IT Asset management)
* **turnkey-syncthing**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 5 GB (File synchronization)
* **turnkey-tomcat / turnkey-tomcat-apache**: RAM 1 GB – 2 GB | CPU 1–2 Core | Disk 5 GB (Java servlet container)
* **turnkey-torrentserver**: RAM 256 MB – 512 MB | CPU 1 Core | Disk 5 GB – 10 GB
* **turnkey-wireguard**: RAM 128 MB – 256 MB | CPU 1 Core | Disk 1 GB (VPN super ringan)
* **turnkey-zencart**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 5 GB
* **turnkey-zoneminder**: RAM 1 GB – 2 GB | CPU 2–4 Core | Disk 20 GB+ (CCTV/Surveillance, sangat berat di CPU dan Disk I/O karena merekam video)
* **turnkey-bugzilla**: RAM 512 MB – 1 GB | CPU 1 Core | Disk 3 GB

"Template ini cuma file instalasi mentah. Mau dia minta RAM 2 GB atau 4 GB, aslinya aplikasi-aplikasi di atas saat pertama nyala rata-rata cuma makan RAM 256MB sampai 512MB doang. Kalau nanti aplikasinya makin ramai dan butuh resource lebih, tinggal kita geser slider-nya di Proxmox tanpa perlu install ulang."
