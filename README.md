# Menjalankan Node Ziesha melalui Systemd

Hardware requirement:
2 CPU
2 RAM
20 SSD

Pertama-tama kita install perangkat yang diperlukan seperti yang dijelaskan pada Ziesha Github Offical

```
sudo apt-get update && sudo apt-get upgrade
```

```
sudo apt install -y build-essential libssl-dev cmake
```

Lalu install Rustup

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Pesan prompt akan muncul silahkan pilih opsi 1 dan tekan ENTER 
 

Setelah instalasi kargo/rustup selesai kita lanjutkan dengan menyimpan semua source ke `"$HOME/.cargo/env"` dengan perintah:
 
```
source "$HOME/.cargo/env"
```


Selanjutnya clone program Ziesha nya:

```
git clone https://github.com/ziesha-network/bazuka
```

```
cd bazuka
cargo install --path .
```

Sekarang node Anda sedang mengkompilasi instalasi Bazuka. Tunggu beberapa saat sampai Instalasi selesai.



# Selanjutnya kita atur layanan Systemd #

Salin seluruh blok kode dan tempel di terminal dan Tekan ENTER

```
echo "[Unit]
Description=Ziesha
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/bazuka
ExecStart=/bin/bash -c 'PATH=$HOME/.cargo/bin:$PATH exec bazuka node start --discord-handle \"add-here-your-discord-id\"'
Restart=always
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
" > $HOME/zieshad.service
```

sekarang edit file  $HOME/zieshad.service untuk menambahkan Discord Anda dengan perintah:

```
nano $HOME/zieshad.service
```

Gunakan ArrowKey/Arah keyboard untuk pindah ke bagian kode **add-here-your-discord-id** hapus tulisannya dan ubah ke Discord-ID punya Anda.
simpan dengan: `CTRL X lalu Y dan ENTER`

Pindahkan file $HOME/zieshad.service di bawah direktori systemd:

```
sudo mv $HOME/zieshad.service /etc/systemd/system
```



Selanjutnya kita periksa apakah program yang kita jalankan semuanya baik-baik saja

```
cat /etc/systemd/system/zieshad.service
```


kalau dirasa tidak ada error lanjut ke langkah berikutnya yaitu mengaktifkan layanan systemd

Aktifkan layanan ziehad untuk boot dengan perintah:

```
sudo systemctl enable /etc/systemd/system/zieshad.service
```


# Sekarang mari kita mulai node 
 
(script berikut menggunakan jaringan pelmeni-7, untuk testnet di masa mendatang cukup ganti namanya aja pake perintah `nano $HOME/zieshad.service`)



**Gunakan salah satu Opsi berikut untuk memulai simpul. (tolong jangan keduanya)**



PILIHAN 1:
jika Anda memiliki Phase Mnemonic, gunakan perintah ini dan ubah **PHASE MNEMONIC LAMA ANDA** dengan milik Anda dan jangan lupa pakai tanda petiknya.

```
bazuka init --network pelmeni-7 --external $(wget -qO- eth0.me):8765 --bootstrap 65.108.193.133:8765 --bootstrap 213.14.138.127:8765 --mnemonic "FRASE MNEMONIK LAMA ANDA"
```

PILIHAN 2:
jika Anda belum memiliki Phase Mnemonic dan ingin dibuatkan yang baru oleh sistem, Jalankan perintah ini:

```
bazuka init --network pelmeni-7 --external $(wget -qO- eth0.me):8765 --bootstrap 65.108.193.133:8765 --bootstrap 213.14.138.127:8765
```


**Terakhir, Mulai Node kita dengan systemd**

Muat ulang daemon

```
sudo systemctl daemon-reload
```

Restart daemon kita

```
sudo systemctl restart ziehad
```

Langkah terakhir untuk melihat Logs, Menandakan Node Anda berjalan dengan baik

```
sudo journalctl -u zieshad -f
```


Jika Anda mengikuti semua langkah, Anda akan melihat log node berjalan dengan baik sekarang.
penginstalan selesai dan node Anda berjalan



**perintah penting untuk mengelola layanan Node ziehad**

```
sudo systemctl daemon-reload
sudo systemctl status ziehad
sudo systemctl start ziehad
sudo systemctl stop ziehad
sudo systemctl restart ziehad
sudo journalctl -u zieshad -f
```



# Apabila selanjutnya terdapat pembaharuan Node ataupun merubah Networknya, berikut perintah sederhana yang bisa digunakan.

pertama mari kita hentikan simpul Anda dengan:

```
sudo systemctl stop ziehad
```

Masuk ke direktori bazuka untuk memutakhirkan/memperbarui node dengan perintah:

```
cd bazuka
git pull origin master
cargo update
cargo install --path .
```

Opsional: ketika Jaringan berubah (pelmeni-*) Anda dapat mengedit .bazuka.yaml tanpa membukanya, gunakan kode ini:

`sed -i "s/<PELMENI SEBELUMNYA>/<PELMENI TERBARU>/g" $HOME/.bazuka.yaml`

Contoh:

```
sed -i "s/pelmeni-6/pelmeni-7/g" $HOME/.bazuka.yaml
```

Opsi lain bisa diubah melalui:
```
sudo nano $HOME/.bazuka.yaml
```

Tanya di discord di channel #node-chat pelmeni versi terbaru apa.



setelah semuanya dirasa sudah benar, muat ulang daemon dan mulai ulang/mulai node:

```
sudo systemctl daemon-reload
sudo systemctl restart ziehad
```


Ingin melihat log Lagi, jalankan:

```
sudo journalctl -u zieshad -f
```

ingin melihat status node dan address wallet Anda?

```
sudo bazuka node status
```

Itu aja, Selamat Bersenang-senang.
Jangan lupa daget kalo cair

Thanks to <a href=https://gist.github.com/AkiAfroo>AkiAfroo</a>
