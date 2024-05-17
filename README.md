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

# STAKE İŞLEMİ

Liteseed node kurduktan sonra stake işlemini yapmalısınız.
Test tokenleriniz de geldiğinde stake adımına buradan devam edebilirsiniz.

Uyarı : Öncelikle eğer her hangi bir domain ile stake ettiyseniz unstake edin. 
Unstake komutu
```
sudo docker run -v liteseed:/data edge unstake "eskidomain"
```

```success```çıktısı almalısınız.

Balance kontrolü yapın 1000 tokeniniz geri gelmiş ve Staked No yazmış olmalı.
```
sudo docker run -v liteseed:/data edge balance
```

Gelelim yeni adımlara, bir adet domaine ihtiyacınız var. Ben namecheap kullanıyorum, basit anlaşılır. Hali hazırda domaininiz varsa subdomain ekleyerek de işlemlere devam edebilirsiniz.
Domain paneline giriyoruz ve ```Manage```tıklıyoruz

![image](https://github.com/neuweltgeld/liteseed/assets/101174090/2ace7c15-eaaa-4a6c-acd1-8d0acfa45ff3)

Advanced DNS sekmesine giriyoruz ve gerekli ayarlara başlıyoruz. Öncelikle 1 numaradaki gibi A record ekliyoruz. Subdomain ile kullanacaksanız host kısmına ```liteseed```ya da istediğiniz bir isim verebilirsiniz. Value olarak sunucu ip girip onaylıyoruz.

Terminale dönüp certbot kurulumu yapalım.
```
sudo apt install -y certbot nginx
```

Config yapılandırmasını başlatıyoruz. emailadresi ve domain kısmını değiştirin. Subdomain kullandıysanız subdomainle beraber yazın.

```
sudo certbot certonly --manual --preferred-challenges dns \
--email <emailadresi> \
-d <domain>
```

Komutu girdikten sonra 2 kez yes diyoruz ve size bir TXT key verecek. Bu keyi yukarıdaki resimde 2 nolu gösterdiğim gibi girmemiz gerek. Ben ```liteseed```subdomaini kullanıyorum. Ona göre ayarları şöyle;

Type : TXT record

Host : _acme-challenge.liteseed

Value: Size verilen TXT key

Onayladıktan sonra 5dk bekleyin. Sonra şu adreste ```https://dnschecker.org/#TXT/``` domaini ```_acme-challenge.domain``` şeklinde aratıp size verilen key dağıtılmış mı kontrol edin.

Yeşil tikleri gördünüz, terminale dönüp enter diyoruz ve başarılı olduğuna dair çıktı alıyoruz.

Sonraki aşama ```ngix```ayarı

```
sudo nano /etc/nginx/sites-available/default
```

içeriğini komple siliyoruz. Kısa yol olarak ctrl + k basılı tutun.

Sonra şu koddaki domain adresini (subdomain kullandıysanız subdomain olacak şekilde) ```<>``` işaretleri olmadan düzenleyin. 4 yerde adres değiştiriyorsunuz.

```
# Force redirects from HTTP to HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name <domain>;

    location / {
        return 301 https://$host$request_uri;
    }
}

# Forward traffic to your node and provide SSL certificates
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name <domain>;

    ssl_certificate /etc/letsencrypt/live/<domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<domain>/privkey.pem;

    location / {
        proxy_pass http://localhost:8080; # or your port you changed at 6.
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
    }
}
```

Kaydedip çıkıyoruz.

```
sudo nginx -t
```
OK çıktısı vermeli

Devamında
````
sudo service nginx restart
````

Diyerek restart atıyoruz. Bu aşamada domaine girdiğinizde bu şekilde görünmeli. Eğer görünmüyorsa bir yerde hata olmuştur.

<img width="484" alt="image" src="https://github.com/neuweltgeld/liteseed/assets/101174090/2e43976e-ae8d-4cb6-9213-7332def29037">

Gelelim son adım stake olayına. Domain kısmını değiştirin.

```
sudo docker run -v liteseed:/data edge stake -u "domain"
```

İşlemler bu kadar. Takıldığınız yer olursa rc de sorabilirsiniz.


