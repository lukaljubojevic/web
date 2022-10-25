# Demonstrature RM

## Kontakt

Za sva pitanja:
[Boomerbook](https://www.facebook.com/luka.ljubojevic.946/)
[Whatsapp](tel:0955293804)
[Instagram](https://www.instagram.com/lukaljubojevic1/)
Iza 22h

## Instalacija Linuxa

1. [Resize disk](https://docs.kde.org/trunk5/en/partitionmanager/partitionmanager/howto-resizepartition.html) -- ovo zna dugo trajati..
    * Dobro pazite koji disk resizate, ako piše tip particije Bitlocker nemojte ništa raditi nego mi se javite!!!
    * Idealno bi bilo da kupite prijenosni HDD ili SSD
        * [Portable SSD 600kn](https://www.links.hr/hr/ssd-vanjski-512-gb-verbatim-store-n-go-usb-3-2-crni-051410066) - bolje
        * [Portable HDD 420kn](https://www.links.hr/hr/tvrdi-disk-vanjski-1000-gb-seagate-expansion-portable-stkm1000400-2-5-usb-3-0-crni-051520707) - gore, ali ga možete particionirati tipa 100GB za Linux, ostalo cca 840GB NTFS particija za masovnu pohranu podataka
2. Particioniranje u instalacijskom programu
    * Ako imate više particija i Windowse na njima, od resizeanog prostora stvorite particije:
        * 300MB, tip: FAT32, mount point: /boot/efi, flags: boot
            * SAMO ZA SUSTAVE KOJI SU EFI (Kako znate? Najčešće imate Windows particiju tipa FAT32 od 100MB) --- na tu EFI particiju od Windowsa (100MB) dodati mount point /boot/efi i flag boot ali pazite da je checkirano KEEP a ne FORMAT!
            * Ako ste kupili prijenosni disk ili imate prazan disk u laptopu/PC-u onda particija je 300MB fat32 format /boot/efi flag boot
        * Koliko god imate MB, tip: ext4/btrfs, mount point: /, no flags
3. Instalirajte Linuxe kako instalacijski program već ide..

## Instalacija softvera

### Ubuntu/Mint

```shell
sudo apt-get update
sudo apt-get install 
sudo apt-get install bash bridge-utils ebtables iproute2 libev-dev python-is-python3 libtk-img xterm mgen traceroute
git clone https://github.com/coreemu/core.git
cd core
./setup.sh
source ~/.bashrc
inv install
sudo systemctl enable --now core-daemon.service
sudo systemctl start core-daemon.service
sudo apt-get install wireshark-qt

**TODO ostali paketi**
```

### Manjaro

```shell
sudo pacman -S --needed base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

paru -S tkimg
paru -S mgen
paru -S core 
sudo pacman -S openssh inetutils net-tools iproute2 traceroute iw wireless_tools bridge-utils iptables-nft nftables quagga dhclient dhcp wireshark-qt gnuplot siege curl curlie php xdebug jq
sudo usermod -aG wireshark (whoami)
sudo systemctl enable --now core-daemon.service
sudo reboot now
sudo systemctl start core-daemon.service
```

* Za garudu po principu za Manjaro bez prvog bloka naredbi za paru!
* Ako paru -S core faila, nema veze samo nastavite

### Virtualizacija - za one kojiima Linux neće

[VMWare Player](https://customerconnect.vmware.com/en/downloads/info/slug/desktop_end_user_computing/vmware_workstation_player/16_0) - moja preferencija
[Virtualbox](https://www.virtualbox.org/wiki/Downloads)

* Imajte na umu da morate uključiti Intel VT-x/VT-d u BIOSu, analogno Virtualization Technology na AMD-ima.
[Manjaro](https://manjaro.org/download/)
[What dis? Kako se to radi?](https://www.youtube.com/watch?v=ZDVLEJVt4e0)
[What?? VT-d??](https://www.geeksforgeeks.org/how-to-enable-virtualization-vt-x-in-windows-10-bios/)
* Ako već idete na virualizaciju odaberite Manjaro/Garudu (Arch), možda bolje XFCE desktop okruženje brže će raditi..

## Demonstrature 1 - Snimanje mrežnog prometa

* Kad tražite sučelje za Wireshark pogledajte traženi router, crveni link između tog i drugog traženog, pa onda wireshark na IP adresu koju vidite kog prvog rutera
* Display filteri su u tražilici
* Capture filteri su u meniju Capture - Capture filters - + - dodajte - pa ponovno pokrenite snimanje
* Za snimanje prometa MORAJU BITI UKLJUČENI TOKOVI u core-u

### Sintaksa naredbi za display filter

* Port:

```shell
protokol.(src/dst)port==PORT or protocol.(src/dst)port==PORT
```

* Ako je u pitanju IP adresa:

```shell
ip.src/dst==IP or ip.src/dst==IP
```

* Ako tražite sve osim nečega:

```shell
!(ip.src==IP) 
!(protoko.(src/dst)port==PORT or ...)
```

* Ako tražite jednu vrstu paketa

```shell
protokol
```

Kombinirajte..

### Sintaksa naredbi za capture filter

```shell
protokol src/dest net IPADDR src/dest net IP ADDR
   npr... icmp src net 10.0.6.11 dst net 10.0.1.20
   
protokol src/dest port PORT
   npr... udp dst port 4000
```

### Traženje paketa koji pripada toku X

* Pogledajte koji su src i dst čvorovi u toku i display filtrirajte pakete t.d.: ip.src == IPpočetnogNODA and ip.dst == IPzavršnogNODA... uzmite 5. ili 6. iz liste
  * U Wiresharku dvoklik na paket pa idete po kategorijama po ono što se traži
    * Npr.. nešto IP nešto idete u Internel Protocol ver 4 sekciju itd.

### RX,TX pitanje

* saznajte količinu primljenih (received, RX) i poslanih (transmitted, TX) podataka na ruteru i pročitajte vrijednosti

```shell
netstat -ie 
```

## Komunikacija HTTP klijenta i poslužitelja (cURL, PHP)

* Treba nam curl
  * [Linux: man curl](https://curl.se/docs/manpage.html)
  * Parametri:

```shell
-X, --request <method>   Specify request method to use
-v, --verbose            Make the operation more talkative
-i, --include            Include protocol response headers in the output
-k, --insecure           Allow insecure server connections (npr. istekli HTTPS certifikati)
-H, --header <header/@file> Pass custom header(s) to server
-w, --write-out <format> Use output FORMAT after completion (npr 15 zahtjeva)
-d, --data <data>        HTTP POST data
-c, --cookie-jar <filename> Write cookies to <filename> after operation
-o, --output <file>      Write to file instead of stdout
-A, --user-agent <name>  Send User-Agent <name> to server
-I, --head               Show document info only
```

* Kako izvesti HTTP zahtjev HEAD za dohvaćanje zaglavlja URL-ova?
  * curl -X [HTTP_REQUEST](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)  -I "URL"
  * Zanimljivo nam je promatrati content-type, server i [HTTP response kodove](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
    * Serveri su često različiti, različite stranice koriste različite web servise na kojima imaju origin. Neki od njih su cloudfare, AWS, GitHub...
  * Primjer outputa:

```shell
curl -X GET -i "https://gaseri.org"
HTTP/2 200 
server: GitHub.com
content-type: text/html; charset=utf-8
last-modified: Wed, 19 Oct 2022 11:38:02 GMT
access-control-allow-origin: *
etag: "634fe19a-294d7"
expires: Tue, 25 Oct 2022 10:12:03 GMT
cache-control: max-age=600
x-proxy-cache: MISS
x-github-request-id: 0805:1832:204DD5:2138CA:6357B41B
accept-ranges: bytes
date: Tue, 25 Oct 2022 10:02:55 GMT
via: 1.1 varnish
age: 52
x-served-by: cache-sof1510024-SOF
x-cache: HIT
x-cache-hits: 1
x-timer: S1666692175.266168,VS0,VE1
vary: Accept-Encoding
x-fastly-request-id: d791fb290e210cac14a23993ec8571f82fe54bf2
content-length: 169175
```

* Kako ćemo proslijediti podatke u URL?

```shell
curl -X HTTP_REQUEST URL -d "val1=x" -d "val2=y" -d "val3=z"
```

* Kako ćemo izvršiti zahtjev kao neki user-agent?
  * User agente možemo pronaći [ovdje](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)

```shell
curl -v -A 'MOJ_USER_AGENT' -I URL
```

* Kako ćemo dohvatiti stranicu i njezin cookie?
  * Kao HTTP ga nećemo dobiti jer se cookie generira prema HASH-u korisnika (nastaje jedinstveni URL naše sesije), a on nastaje samo prilikom TLS autentifikacije (pristup HTTPS verziji URL-a).

```shell
curl -c MOJ_COOKIE.txt URL
```

* Kako ćemo napraviti php datoteku t.d. nas, ako je zahtjev GET, ovisno o nekoj putanji, preusmjeri HTTP kodom?

```php
<?php

if ($_SERVER["REQUEST_METHOD"] == "HTTP_REQUEST_METODA" && $_SERVER["REQUEST_URI"] == "/PUTANJA NA KOJU DOLAZIMO") {
    http_response_code(TRAŽENI_RESPONSE_KOD);
    header("Location: PUTANJA NA KOJU REDIRECTAMO");
} 
```

* Kako ćemo napraviti php datoteku t.d. nam javi poruku o grešci ako, neautorizirani, izvršimo request "POST"

```php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
$KLJUČ_AUTORIZACIJE=vrijednost
$PROVJERA_AUTORIZACIJE = $_POST["PROVJERA_AUTORIZACIJE"];
    if ($KLJUČ_AUTORIZACIJE==$PROVJERA_AUTORIZACIJE){
      http_response_code(TRAŽENI_RESPONSE_KOD_AKO_SMO_AUT);
    } else{
      http_response_code(TRAŽENI_RESPONSE_KOD_AKO_NISMO_AUT);
}}
?>
```

* Kako pokrenemo php datoteku?
  * Kao output dobiti ćemo lokaciju na kojoj se poslužuje datoteka (najčešće localhost:8080/80)

```shell
php moj_php.php
```

* Kako proslijediti podatke u PHP?

```shell
curl -vLX POST -d "val1=x" -d "val2=y" URL
```

* Kako provjeriti zahtjev nad PHP-om?

```shell
curl -vLX GET URL
```

* Kako u PHP-u dohvatiti vrijednost metodom GET i provjeriti je li vrijednost postavljena?

```php
$vrijednost=$_GET["vrijednost"];
if (!isset(vrijednost)){
    moje naredbe;
}
```

* Kako ispisati poruku u PHP-u?

```php
echo ""<p>Moja poruka</p>\n";
```
