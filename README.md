# Axelar-Testnet
Axelar testnet için türkçe rehber.

## <a name='İçerik'></a>İçerik

* [1. Gereksinimler](#1-gereksinimler)
* [2. Kurulum Adımları](#2-kurulum-adımları)
* [3. Egzersiz 4](#3-egzersiz-4)

## 1. Gereksinimler

**Minimum** gereksinimler aşağıda ki gibidir:
 - **CPU**: 4 Core işlemci
 - **RAM**: 8GB ram
 - **Depolama**: 512GB depolama

**Yardımcı** linker:

[Axelar Musluğu](http://faucet.testnet.axelar.dev/)

[Axelar core](https://hub.docker.com/repository/docker/axelarnet/axelar-core)

[Tofnd](https://hub.docker.com/repository/docker/axelarnet/tofnd)

[Go](https://golang.org/doc/install)

[Axelar gerekli adresler ve sürümler](https://docs.axelar.dev/#/resources/testnet-releases)

## 2. Kurulum Adımları

Bağımlılıkları yüklüyoruz
```
apt-get install jq
apt-get install build-essential
```
Docker kuruyoruz
```
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Komut dosyalarını ve yapılandırmaları kullanmak için depoyu klonluyoruz : 

screen -S axelar

git clone https://github.com/axelarnetwork/axelarate-community.git
cd axelarate-community

docker pull axelarnet/axelar-core:v0.13.0

docker pull axelarnet/tofnd:v0.9.1
Biz rehberi hazırladığımız da, axelar-core v13, tofnd v0.9.1. Siz kurulum yaparken, bu sürümler değişmiş/güncellenmiş olabilir. Bunu üstte Yardımcı linkler olarak belirttiğimiz kısımda, docker imajları adı altında ki linkleri kullanarak kontrol edebilirsiniz. 

Bu linke tıklayın ve "axelarate-community version" karşısında ki sürümü aşağıda ki Release Tag kısmına girin, ve adımı tamamlayın.

git checkout <release-tag>
External İp adresinizi not edin, config.toml dosyasını düzenleyeceğiz. 

cd join
nano config.toml
Aşağıya doğru inin ve external_address = " " satırını bulun, not ettiğiniz external ip'nizi ;

external_address = "123.123.123.123:26656"
Biçiminde doldurun.1317, 26656-26658 ve 26660 portlarını kullanmanız önerilir. 



Şimdi, node'umuzu çalıştıralım 

./join-testnet.sh


Şuna benzer bir çıktı almalısınız

Axelar node running.
Validator address: axelarvaloper1ttxxytlz377agnvzqhllzxmg7dd76tnrwzyahz
- name: validator
  type: local
  address: axelar1ttxxytlz377agnvzqhllzxmg7dd76tnrwrjc9d
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"A+O6nUmpQs1meQLtr2RaG5DExv1nyU9cQJKeAUJNH828"}'
  mnemonic: ""
**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.
naive segment sword error champion pyramid world spend tool reason sound hub barrel amazing parade ahead lamp flag disorder sunny loop artist almost expire
Do not forget to also backup the tendermint key (/Users/talalashraf/.axelar_testnet/.core/config/priv_validator_key.json)
To follow execution, run 'docker logs -f axelar-core'
To stop the node, run 'docker stop axelar-core'


Şimdi düğümümüzün senkronize olmasını bekleyeceğiz. Senkronize olup olmadığını şu adımları takip ederek öğrenebilirsiniz. 

curl localhost:26657/status | jq '.result.sync_info'


Ardınan şuna benzer bir çıktı almalısınız 

{
"latest_block_hash": "0B64D2A0EDAB6CEF229510E52F137130134D94AAD64EACB553D51D01B0D1A446",
"latest_app_hash": "FA3730F49F491DCFF38687F2603CF154563AFA9C77331AF75340C554CB555EFC",
"latest_block_height": "17051",
"latest_block_time": "2021-06-01T23:41:43.161261874Z",
"earliest_block_hash": "080E6B9FC64778F3E0671E046575D3460984F5B1F584E1F2D467341061C7627A",
"earliest_app_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
"earliest_block_height": "1",
"earliest_block_time": "2021-05-31T21:05:12.032466392Z",
"catching_up": true
}


Bir süre sonra yeniden kontrol sağlayın "catching_up": false olduğunda, Senkronizasyon tamamnlanmış demektir. 

Sync tamamlandıktan sonra devam edin!



Loglarımızı rahatça kayıt edebilmek için aşağıda ki komutu çalıştıralım

docker logs -f axelar-core 2&> testnet.log
Logları testnet.log adlı dosyaya yönlendirdik, şimdi bu logları görüntülemek için artık şu kodu kullanmanız yeterli 

tail -f testnet.log


Axelarda bir anahtar oluşturup testnet jetonlarını talep ediyoruz

CTRL A C tuş kombinasyonuyla yeni bir  terminal penceresi açalım

docker exec -it axelar-core sh

axelard keys show validator -a
Bu linkten biraz token alıyoruz

axelard q bank balances {bir önceki adımdaki çıktıda yer alan adres}
Örnek 

axelard q bank balances axelar1hk3xagjvl4ee8lpdd736h6wcwsudrv0f5ya2we
