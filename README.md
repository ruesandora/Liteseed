# Liteseed

> AO ve Arweave ekosisteminden elde ettiğimiz faydalar net bir şekilde ortada - Liteseed kurulumu kolay eksik kalmasın.

> Donanım yazmıyorum, herhangi bir sunucunuzun yanına kurabilir veya minimal bir cihaz tercih edilebilir.

> Teşvikli mi? Evet ama detay yok kesin demiyorum discord duyuruda yazıyor - taktir sizindir.

> Kolay kurulum için mümkün mertebe kısa ve özet yazıyorum..

## Kurulum:
```console
# güncelleme
sudo apt update -y && sudo apt upgrade -y
sudo apt install docker.io -y

git clone https://github.com/liteseed/edge

cd ./edge
docker build -t edge .

sudo docker volume create liteseed

# bir key oluşturacağız burada daha sonra kaydetmeyi göstereceğim.
docker run -v liteseed:/data edge generate
docker run -v liteseed:/data edge migrate

screen -S liteseed
docker run -v liteseed:/data edge start
# akabinde ctrl a d ile çıkalım.

# bu komutla cüzdan adresimizi öğrenip test tokeni isteyelim
docker run -v liteseed:/data edge balance
# buradaki cüzdana hello@liteseed.xyz mail adresıne göndererek token isteylim.
```

> Buradan sonra token geldikten sonra devam edebilirsiniz.


```console
# Burada tırnakların içersinde bir github veya twitter bağlantını koy.
docker run -v liteseed:/data edge stake -u "https://ruesandora.com"

# bu seger bu komutu girdiğinizde token elinizde kalmayacak ve stake kısmı Yes olacaktır.
docker run -v liteseed:/data edge balance

# screen içersine girip durdurup tekrar başlatalım.
screen -r liteseed
docker run -v liteseed:/data edge start
```

<img width="832" alt="Ekran Resmi 2024-05-16 15 49 59" src="https://github.com/ruesandora/Liteseed/assets/101149671/f684c11b-6c78-46c9-927e-40904e9eedf4">

> Böyle bir görsel node'unuzun doğru çalıştığını temsil eder.

> Hayırlı olsun, proje adına büyük konuşmak için çok erken - beklentisiz ilerleyiniz.

```console
cd /var/lib/docker/volumes/liteseed/_data
cat signer.json
# bu komut ile private keyinizi yedekleyebilirsiniz.
```

