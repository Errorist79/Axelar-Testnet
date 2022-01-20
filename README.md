# Axelar-Testnet
Axelar testnet için türkçe rehber.

## <a name='İçerik'></a>İçerik

* [1. Gereksinimler](#1-gereksinimler)
* [2. Kurulum Adımları](#2-kurulum-adımları)
* [3. Egzersiz 4](#3-egzersiz-4)
    * [3.1 Varlıkları Terra testnet'ten EVM uyumlu zincirlere gönderme](#31-varlıkları-terra-testnetten-eVM-uyumlu-zincirlere-gönderme)
    * [3.2 Varlıkları Terra'ya geri gönder](#32-varlıkları-terraya-geri-gönder)

## 1. Gereksinimler

**Minimum** gereksinimler aşağıda ki gibidir:
 - **CPU**: 4 Core işlemci
 - **RAM**: 8GB ram
 - **Depolama**: 512GB depolama

**Yardımcı** linker:

- [Axelar Musluğu](http://faucet.testnet.axelar.dev/)

- [Axelar core](https://hub.docker.com/repository/docker/axelarnet/axelar-core)

- [Tofnd](https://hub.docker.com/repository/docker/axelarnet/tofnd)

- [Go](https://golang.org/doc/install)

- [Axelar gerekli adresler ve sürümler](https://docs.axelar.dev/#/resources/testnet-releases)

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
```
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Komut dosyalarını ve yapılandırmaları kullanmak için depoyu klonluyoruz : 
```
screen -S axelar

git clone https://github.com/axelarnetwork/axelarate-community.git
cd axelarate-community

docker pull axelarnet/axelar-core:v0.13.0

docker pull axelarnet/tofnd:v0.9.1
```
Biz rehberi hazırladığımız da, axelar-core v13, tofnd v0.9.1. Siz kurulum yaparken, bu sürümler değişmiş/güncellenmiş olabilir. Bunu üstte Yardımcı linkler olarak belirttiğimiz kısımda, docker imajları adı altında ki linkleri kullanarak kontrol edebilirsiniz. 

[Bu linke tıklayın](https://docs.axelar.dev/#/resources/testnet-releases) ve "axelarate-community version" karşısında ki sürümü aşağıda ki Release Tag kısmına girin, ve adımı tamamlayın.
```
git checkout <release-tag>
```
External İp adresinizi not edin, config.toml dosyasını düzenleyeceğiz. 
```
cd join
nano config.toml
```
Aşağıya doğru inin ve **external_address = " "** satırını bulun, not ettiğiniz external ip'nizi ;

`external_address = "123.123.123.123:26656"`
Biçiminde doldurun.**1317, 26656-26658 ve 26660** portlarını kullanmanız önerilir. 



Şimdi, node'umuzu çalıştıralım 
```
./join-testnet.sh
```

Şuna benzer bir çıktı almalısınız
```
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
```

Şimdi düğümümüzün senkronize olmasını bekleyeceğiz. Senkronize olup olmadığını şu adımları takip ederek öğrenebilirsiniz. 
```
curl localhost:26657/status | jq '.result.sync_info'
```

Ardınan şuna benzer bir çıktı almalısınız 
```
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
```

Bir süre sonra yeniden kontrol sağlayın **"catching_up":** false olduğunda, Senkronizasyon tamamnlanmış demektir. 

Sync tamamlandıktan sonra devam edin!



Loglarımızı rahatça kayıt edebilmek için aşağıda ki komutu çalıştıralım
```
docker logs -f axelar-core 2&> testnet.log
```
Logları testnet.log adlı dosyaya yönlendirdik, şimdi bu logları görüntülemek için artık şu kodu kullanmanız yeterli 
```
tail -f testnet.log
```

Axelarda bir anahtar oluşturup testnet jetonlarını talep ediyoruz

**CTRL A C tuş** kombinasyonuyla yeni bir  terminal penceresi açalım
```
docker exec -it axelar-core sh
axelard keys show validator -a
```
[Bu linkten](http://faucet.testnet.axelar.dev/) biraz token alıyoruz
```
axelard q bank balances {bir önceki adımdaki çıktıda yer alan adres}
```
**Örnek 
```
axelard q bank balances axelar1hk3xagjvl4ee8lpdd736h6wcwsudrv0f5ya2we
```


## 3. Egzersiz 4




**CTRL A C** tuş kombinasyonuyla yeni bir  terminal penceresi açalım
```
git clone https://github.com/terra-money/core/
cd core
git checkout v0.5.11
make install
```

Yüklemenin kurulduğunu doğrulayalım
```
terrad version --long
```
**-bash: terrad: command not found** hatası alırsanız şunu deneyin 
```
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
source .profile
```

Düğümü başlatıyoruz

[moniker] istediğiniz herhangi bir isim olabilir
```
terrad init [moniker]
```

chain-id ve node parametrelerini düzenlemek için 
```
nano $HOME/.terra/config/client.toml
```
Açılan dosya da parametreleri şu şekilde değiştirin : 
`chain-id = "bombay-12"`
`node = "tcp://adc1043f1d76249009c417dcad0bc807-1055950820.us-east-2.elb.amazonaws.com:26657"`


Statü kontrolü yapalım
```
terrad status
```
Bir anahtar oluşturalım

{terra-key-name} istediğin herhangi bir isim olabilir
```
terrad keys add {terra-key-name}
```

Musluktan token isteyin (UST)

Tokenleri alıp almadığınızı kontrol edin 
```
terrad q bank balances [address]
```

UST'yi Terra testnet'ten EVM uyumlu zincirlere gönderme


**Akış, Axelar'ın desteklediği tüm EVM uyumlu zincirler için çalışır. Örnek olarak Ethereum Ropsten kullanıyoruz.

Axelar docker container'ını açalım, önce axelard kurulu terminale geçelim (CTRL A P tuşuyla terminaller arası geçiş yapmanız mümkün)
```
docker exec -it axelar-core sh
```

## 3.1 Varlıkları Terra testnetten EVM uyumlu Zincirlere gönderme
```
axelard tx axelarnet link [evm ağı] [alııcı adres] uusd --from [axelar-anahtar-adı]
```
Örnek 
```
axelard tx axelarnet link ethereum 0x4c14944e080FbE711D29D5B261F14fE4E754f939 uusd --from validator
```

Çıktı içerisinde şunu arayın : 

`successfully linked [Axelar Network deposit address] and [receipent address]`



### 3.1.1 Terrad kurulu terminalimize geri dönelim. 
```
terrad tx ibc-transfer transfer transfer [Terra channel id] [Axelar Network deposit address] --packet-timeout-timestamp 0 --packet-timeout-height "0-20000" [amount]uusd --gas-prices 0.15uusd --from [terra-key-name] -y -b block
```

Terra channel id Testnet Sürümü altında bulabilirsiniz

 {terra-key-name} yukarıdaki Bir anahtar oluşturalım adlı adımda oluşturduğunuz anahtardır

İşlemin tamamlanması yaklaşık 1 dakika sürer.



### 3.1.2 Axelard terminaline geçin ve tokenleri alıp almadığınızı doğrulayın
```
axelard q bank balances [Axelar Network deposit address]
```

Böyle bir çıktı görmeniz gerekir 
```
balances:
- amount: "1000000"
 denom: ibc/6F4968A73F90CF7DE6394BF937D6DF7C7D162D74D839C13F53B41157D315E05F
```

### 3.1.3 Yatırma işlemini onaylayalım

{txhash} 2. adımdandır

{amount} ve {token} yukarıdaki 2. adımdakiyle aynıdır

{Axelar Network deposit address}, yukarıda para yatırdığınız adrestir.
```
axelard tx axelarnet confirm-deposit [txhash] [amount]"[token]" [Axelar Network deposit address] --from [axelar-key-name]
```

Örnek
```
axelard tx axelarnet confirm-deposit F72D180BD2CD80DB756494BB461DEFE93091A116D703982E91AC2418EC660752  1000000"ibc/6F4968A73F90CF7DE6394BF937D6DF7C7D162D74D839C13F53B41157D315E05F" axelar1gmwk28m33m3gfcc6kr32egf0w8g6k7fvppspue --from validator
```

### 3.1.4 Evm Uyumlu zincirde transfer oluşturup imzalama
```
axelard tx evm create-pending-transfers [chain] --from [key-name] --gas auto --gas-adjustment 1.2
axelard tx evm sign-commands [chain] --from [key-name] --gas auto --gas-adjustment 1.2
```

Örnek
```
axelard tx evm create-pending-transfers ethereum --from validator --gas auto --gas-adjustment 1.2
axelard tx evm sign-commands ethereum --from validator --gas auto --gas-adjustment 1.2
```

Çıktı da şunu bulun

`successfully started signing batched commands with ID {batched commands ID}`



### 3.1.5 Mint işlemini yürütmek için bir işlemde gönderilmesi gereken komut verilerini alalım

```
axelard q evm batched-commands [chain] {batched commands ID 5. adımdaki}
``````

Örnek 
```
axelard q evm batched-commands ethereum 1d097247c283cfaca76ad1de4f3a2e5d4d075d99664e5d87aa187a331e8546e7
```

Bunu görmelisiniz

`status: BATCHED_COMMANDS_STATUS_SIGNED`

Ardından `execute_data` karşısındaki çıktıyı kopyalayın



### 3.1.6 Mint işlemini yürütmek için komut verilerini saran işlemi gönderelim



Metamask cüzdanınızı açın, Ayarlar -> Gelişmiş'e gidin, ardından HEX verilerini göster'i bulun ve bu seçeneği etkinleştirin. Bu şekilde, doğrudan Metamask cüzdanı ile bir veri işlemi gönderebilirsiniz.

Metamask'a gidin, Gateway smart contract address bir işlem gönderin , execute_data da kopyaladığınız içeriği yani hex'i yukarıdan Hex Data alanına yapıştırın 

Herhangi bir jeton transfer etmemeyi unutmayın! Sözleşmeyi yürütürken gaz çıkışı hatası olasılığını azaltmak için, onay ekranında Düzenle'yi seçerek 1000000 gibi daha yüksek bir gaz sınırı ayarlamanızı öneririz.

("Adres"in, Testnet Sürümü altında bulabileceğiniz Axelar Gateway akıllı sözleşmesinin adresi olduğuna dikkat edin )

Artık Metamask'ı açabilir, "Varlıklar"ı, ardından "Tokenleri içe aktar"ı, ardından "Özel Simge"yi seçebilir ve axelarUST belirteç sözleşme adresini yapıştırabilirsiniz (bkz. [Testnet Sürümü](https://docs.axelar.dev/#/resources/testnet-releases) altında ilgili token adresini arayın).



## 3.2 Varlıkları Terraya geri gönder


### 3.1.7 Evm uyumlu zincirde bir para yatırma adresi oluşturun
```
axelard tx evm link [chain] terra [terra address] uusd --from [key-name]
```

Örnek
```
axelard tx evm link ethereum terra terra1syhner2ldmm7vqzkskcflaxl6wy9vn7m873vqu uusd --from validator
```

Çıktı içerisinde şunu bulun 

`successfully linked [Ethereum Ropsten deposit address] and [Terra address]`



### 3.1.8 Harici: sarılmış belirteçleri [Ethereum Ropsten deposit address​​​​​​​] gönderin (örn. Metamask ile). İşlemi göndermek için adreste bir miktar Ropsten testnet Ether'e sahip olmanız gerekir. 30 blok onayı bekleyin. Testnet gezginini kullanarak yatırma durumunuzu izleyebilirsiniz: https://ropsten.etherscan.io/



### 3.1.9 Transferi onaylayın
```
axelard tx evm confirm-erc20-deposit [chain] [txID] [amount] [deposit address] --from [key-name]
```

Örnek
```
axelard tx evm confirm-erc20-deposit ethereum 0xb82e454a273cb32ed45a435767982293c12bf099ba419badc0a728e731f5825e 1000000 0x5CFEcE3b659e657E02e31d864ef0adE028a42a8E --from validator
```

İşlem onayını logları inceleyerek görebilirsiniz
```
docker logs -f axelar-core 2>&1 | grep -a -e "deposit confirmation"
```

### 3.1.10 Axelar Network üzerinde bekleyen IBC transferini yönlendirin
```
axelard tx axelarnet route-ibc-transfers --from [key-name] --gas auto --gas-adjustment 1.2
```
işlem süresi yaklaşık 1 dakikadır. 



### 3.1 Terrad kurulu olan terminale geri dönün, ust aldığınızı doğrulayın

{terra-address} yukarıda kullandığınız adrestir. 
```
terrad q bank balances {terra-address}
```
