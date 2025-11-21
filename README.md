<h1 align="center">Citrea Trusted Setup Verification</h1>

Citrea Risc0-to-BitVM Trusted Setup Ceremony başarıyla tamamlandı! Bu, BitVM’in Bitcoin üzerindeki ZK kanıtları için yapılan ilk trusted setup. Artık Clementine ve diğer BitVM projeleri ortak ve doğrulanabilir kriptografik parametreleri kullanabiliyor. Toplam 63 kişi katkıda bulundu ve tüm Clementine köprü imzalayıcıları da dahil edildi. Ekstra bir güven varsayımı eklenmedi.
> Daha fazla bilgi için [tıklayınız.](https://dev.risczero.com/api/trusted-setup-ceremony)

Aşağıdaki doğrulama, setup’la oynanmadığını ve Clementine’in tam olarak üretilen parametrelere baktığını görmek için önemli. 
Devreyi tekrar derleyip, doğru Powers of Tau ve final zkey’i indirip transcript’i kontrol ederek, taahhütleri yeniden oluşturabilir ve yayımlanan parametreler ve doğrulama anahtarı hash’leri ile karşılaştırabilirsiniz.


<h2 align="center">Sistem Gereksinimleri</h1>

```
> minimum 64 gb ram önerilir.
```

<h2 align="center">Circom & SnarkJS Kurulumu</h1>

```console
# bağımlılıklar
sudo apt-get update
sudo apt-get install -y build-essential git npm

# Rust (circom derlemek için)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"

# circom (v2.2.x)
git clone https://github.com/iden3/circom.git
cd circom
cargo install --path circom
circom --version
cd ..

# snarkjs
npm install -g snarkjs
snarkjs --version

```

<h2 align="center">Citrea RiscZero-to-BitVM deposunu klonla</h1>

```console
git clone https://github.com/chainwayxyz/risc0-to-bitvm2
cd risc0-to-bitvm2
```

<h2 align="center">Git-lfs’i kur ve repodaki büyük dosyaları çek</h1>

```console
sudo apt-get install -y git-lfs
git lfs pull
```

<h2 align="center">r1cs dosyalarını oluştur</h1>

```console
# circomlib
git clone https://github.com/iden3/circomlib.git

# Derle -> verify_for_guest.r1cs
sed -i '$d' ./groth16_proof/circuits/stark_verify.circom
circom groth16_proof/circuits/verify_for_guest.circom --r1cs --O2

# (Opsiyonel Adım) oluşturduğun r1cs’in shasum kontrolü
sha256sum verify_for_guest.r1cs
# c90be1fb4b83f8f10f4be51e47ebdde2fd97dacd60d658d63d6828f3f642a116
```

<h2 align="center">Powers of Tau ve final zkey’i kur</h1>

```console
# seremoni için kullanılan tam dosyaları indir (10+ GB)
wget https://pse-trusted-setup-ppot.s3.eu-central-1.amazonaws.com/pot28_0080/ppot_0080_23.ptau
wget https://static.mainnet.citrea.xyz/verify_for_guest_final.zkey

# (Opsiyonel Adım) bunları da kontrol et
sha256sum ppot_0080_23.ptau verify_for_guest_final.zkey
# 05085994c8aa34e4925585202e178e09fb8acc04e9565311751e8a04dec81cb1  ppot_0080_23.ptau
# 86fc7478fce8b91b502538cc64e8295a104f68ecf025042a21b10b54d7d504c0  verify_for_guest_final.zkey
```

<h2 align="center">SnarkJS ile doğrulama çalıştır</h1>

```console
# büyük devreler için Node hafızasını artır
export NODE_OPTIONS="--max-old-space-size=32768"

# zkey transcript’in r1cs ve ptau ile eşleştiğini doğrula
# Sisteminize bağlı olarak 30 dk-birkaç saate kadar sürebilir
snarkjs zkey verify verify_for_guest.r1cs ppot_0080_23.ptau verify_for_guest_final.zkey
```

> Sonucu Citrea'nın eş kurucusu [Ekrem Bal'ın](https://x.com/ekrembal) paylaşımı ile [karşılaştırabilirsiniz](https://gist.github.com/ekrembal/527f44c91736a511f6bfd61e01bc4f22)
> Bireysel attestasyonları buradan kontrol edebilirsiniz:
> Başarılar...
