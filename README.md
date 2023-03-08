# Tutorial Testnet Mande Chain Node Airdrop Finder

<p style="font-size:14px" align="right">
<a href="https://t.me/airdropfind" target="_blank">Join Telegram Airdrop Finder<img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
</p>

<p align="center">
  <img height="auto" width="auto" src="https://raw.githubusercontent.com/bayy420-999/airdropfind/main/NavIcon.png">
</p>

## Referensi

[Website](https://www.mande.network/)

[Dokumen resmi](https://github.com/mande-labs/testnet-2)

[Server Discord Mande chain](https://discord.gg/2DjpVHDxPe)

[Faucet](https://discord.com/channels/953348696098103366/1033430536129101904)

[Explorer](https://explorer.stavr.tech/mande-chain)

## Persyaratan hardware & software

### Persyaratan hardware

| Komponen | Spesifikasi minimal |
|----------|---------------------|
|CPU|4 Cores|
|RAM|6 GB DDR4 RAM|
|Penyimpanan|100 GB HDD|
|Koneksi|10Mbit/s port|

| Komponen | Spesifikasi rekomendasi |
|----------|---------------------|
|CPU|32 Cores|
|RAM|32 GB DDR4 RAM|
|Penyimpanan|2 x 1 TB NVMe SSD|
|Koneksi|1 Gbit/s port|

### Persyaratan software/OS

| Komponen | Spesifikasi minimal |
|----------|---------------------|
|Sistem Operasi|Ubuntu 16.04|

| Komponen | Spesifikasi rekomendasi |
|----------|---------------------|
|Sistem Operasi|Ubuntu 20.04|


## Setup
### Install dependensi
1. Install wget, curl, git
   > Kalo udah pernah install wget/curl/git skip langkah ini
   ```console
   apt-get update && apt-get upgrade 
   apt-get install wget curl git 
   ```
2. Install Go
   > Kalo udah pernah install Go skip langkah ini
   ```console
   wget https://go.dev/dl/go1.19.1.linux-amd64.tar.gz
   rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.1.linux-amd64.tar.gz
   export PATH=$PATH:/usr/local/go/bin
   ```
3. Install Ignite
   ```console
   curl https://get.ignite.com/cli! | bash
   ```

### Download dan install Node
1. Download Node
   ```console
   git clone --branch v1.2.2 https://github.com/mande-labs/mande-chain.git
   ```
2. Masuk ke folder Node
   ```console
   cd mande-chain
   ```
3. Build Node
   ```console
   ignite chain build --release
   ```
   Nanti ada prompt kalian ketik y terus enter
4. Extract Node yang sudah kalian build
   ```console
   cd release && tar -xvf mande-chain_linux_amd64.tar.gz
   mv mande-chaind /usr/local/bin/mande-chaind
   ```
### Menjalankan Node
1. Buat wallet
   ```console
   mande-chaind keys add wallet
   ```
   > Copy mnemonic dan addressnya

2. Claim faucet
   Claim faucet di channel #faucet-requests di discord, format:
   ```console
   $request <address> theta
   ```
   Contoh:
   ```console
   $requet mande1ykfvxzqngpzmw5u4cdsrx607they757gqle6sw theta
   ```
3. Inisialisasi Node
   ```console
    mande-chaind init <NAMA_VALIDATOR> --chain-id mande-testnet-2
   ```
4. Update genesis
   ```console
   wget https://raw.githubusercontent.com/mande-labs/testnet-2/main/genesis.json -O $HOME/.mande-chain/config/genesis.json
   ```
5. Update persistent peers
   ```console
   nano $HOME/.mande-chain/config/config.toml
   ```
   Scroll kebawah cari `persistent_peers = ""`, ganti jadi
   ```console
   persistent_peers = "dbd1f5b01f010b9e6ae6d9f293d2743b03482db5@34.171.132.212:26656,1d1da5742bdd281f0829124ec60033f374e9ddac@34.170.16.69:26656"
   ```
   Save file dengan CTRL + x + y + enter
6. Update minimum gas
   ```console
   nano $HOME/.mande-chain/config/app.toml
   ```
   Scroll kebawah cari `minimum-gas-prices = ""`, ganti jadi
   ```console
   minimum-gas-prices = "0.005mand"
   ```
   Save file dengan CTRL + x + y + enter
7. Buat daemon
   ```console
   sudo tee /etc/systemd/system/mande-chaind.service > /dev/null <<EOF
   [Unit]
   Description=mande-chaind
   After=network-online.target
   [Service]
   User=$USER
   ExecStart=$(which mande-chaind) start
   Restart=always
   RestartSec=3
   LimitNOFILE=4096
   [Install]
   WantedBy=multi-user.target
   EOF
   ```
8. Jalankan Node
   ```console
   systemctl enable mande-chaind && systemctl start mande-chaind
   ```

### Daftar validator
1. Pastikan Node sudah sync
   ```console
   mande-chaind status | jq
   ```
   Jika `catching_up: false` lanjut ke langkah berikutnya
2. Daftar validator
   ```console
   mande-chaind tx staking create-validator \
   --from wallet \
   --amount 0cred \
   --pubkey "$(mande-chaind tendermint show-validator)" \
   --chain-id mande-testnet-2 \
   --moniker="<NAMA_VALIDATOR>" \
   --gas=auto \
   --gas-adjustment=1.15
   ```

## Perintah berguna

### Menghentikan Node
```console
systemctl stop mande-chaind
```

### Restart Node
```console
systemctl restart mande-chaind
```

### Hapus Node
```console
systemctl stop mande-chaind && systemctl disable mande-chaind
rm -rf mande-chain .mande-chain /usr/local/bin/mande-chaind
```

### Cek log
```console
journalctl -fu mande-chaind -o cat
```

### Cek status Node
```console
mande-chaind status | jq
```

### Cek valoper address
```console
mande-chaind keys show wallet --bech val -a
```

## Troubleshoot
Reserved
