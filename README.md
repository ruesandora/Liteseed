# Liteseed

> AO ve Arweave ekosisteminden elde ettiğimiz faydalar net bir şekilde ortada - Liteseed kurulumu kolay eksik kalmasın.

> Donanım yazmıyorum, herhangi bir sunucunuzun yanına kurabilir veya minimal bir cihaz tercih edilebilir.

> Teşvikli mi? Evet ama detay yok kesin demiyorum discord duyuruda yazıyor - taktir sizindir.

## Kurulum:

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

