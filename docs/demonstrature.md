# Demonstrature RM

## Kontakt

Za sva pitanja:
* [Whatsapp](tel:0955293804)
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

## HTTP autentifikacija u jeziku PHP

* HTTP Basic autentifikacija koristi base64 enkodirano korisničko ime i lozinku, naravno mi možemo i kriptirati naše podatke kojim god cipherom želimo..:
  * više na [MMOS](https://gaseri.org/hr/nastava/kolegiji/MMOS/) kolegiju

```shell
openssl enc -chiphers
```

Kodiranje base64:

```shell
echo -n "encode me" | openssl enc -base64
ZW5jb2RlIG1lCg==
```

Dekodiranje base64:

```shell
echo "ZW5jb2RlIG1lCg==" | openssl enc -base64 -d
```

Enkripcija [AES-256](https://www.ipswitch.com/blog/use-aes-256-encryption-secure-data) algoritmom:

```shell
echo "korisnik@korisnik.hr" | openssl enc --aes-256-ctr -a 

enter AES-256-CTR encryption password: mojTajniPass
U2FsdGVkX1/Ur0/Zu2FDjsy0JJOza58AXskxvEViOwEQ2hpVhA==
```

Kako ćemo napraviti autorizaciju PHP kodom?

```php
<?php

$request_headers = getallheaders();
if (array_key_exists("Authorization", $request_headers) && $request_headers["Authorization"] == "U2FsdGVkX1/Ur0/Zu2FDjsy0JJOza58AXskxvEViOwEQ2hpVhA==") {
    echo "<p>Tajni podaci NASA USA CIA</p>\n";
} else {
    http_response_code(401);
    echo "<p>Ruski hakleru bizi!</p>\n";
}
```

Pokrenimo kod i testirajmo:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop$ mkdir public
lljubojevic@IdeaPad5-Pro:~/Desktop$ mv moj2php.php public/index.php
lljubojevic@IdeaPad5-Pro:~/Desktop$ php -S localhost:8000 -t public
```

Uz prave podatke:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -H "Authorization: U2FsdGVkX1/Ur0/Zu2FDjsy0JJOza58AXskxvEViOwEQ2hpVhA==" http://localhost:8000/
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET / HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
> Authorization: U2FsdGVkX1/Ur0/Zu2FDjsy0JJOza58AXskxvEViOwEQ2hpVhA==
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:18:34 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
< Content-type: text/html; charset=UTF-8
< 
<p>Tajni podaci NASA USA CIA</p>
* Closing connection 0
```

Uz krive podatke:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -H "Authorization: grinc" http://localhost:8000/
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET / HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
> Authorization: grinc
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 401 Unauthorized
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:18:15 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
< Content-type: text/html; charset=UTF-8
< 
<p>Ruski hakleru bizi!</p>
* Closing connection 0
```

[gaseri](gaseri.org) - Implementacija autentifikacije
> RFC 7235 naslovljen Hypertext Transfer Protocol (HTTP/1.1): Authentication definira općeniti autenfikacijski okvir na način:
>Klijent šalje zahtjev za resursom, a poslužitelj šalje odgovor sa statusnim kodom 401 Unauthorized ... koji sadrži informacije o tome kako se autentificirati i zaglavlje WWW-Authenticate s najmanje jednim izazovom...
>Klijent koji se želi autentificirati s poslužiteljem će poslati zahtjev koji sadrži zaglavlje Authorization s tipom vjerodajnicama autorizacije.
Kako to radi u PHP-u? [gaseri](gaseri.org)
> U jeziku PHP moguće je implementirati HTTP autentifikaciju korištenjem funkcije header() za slanje HTTP zaglavlja WWW-Authenticate s odgovarajućom vrijednosti i, kao i ranije, funkcije http_response_code() za postavljanje statusnog koda 401 Unauthorized u odgovoru na zahtjev.
> Nakon primanja idućeg zahtjeva koji sadrži zaglavlje Authorization s odgovarajućom vrijednosti, u polju $_SERVER su dodane vrijednosti $_SERVER["AUTH_TYPE"], $_SERVER["PHP_AUTH_USER"] i $_SERVER["PHP_AUTH_PW"] koje sadrže tip autentifikacije, korisničko ime i zaporku (respektivno).

PHP autentifikacija

```php
<?php
if ($_SERVER["PHP_AUTH_USER"] == 'ilijacvorovic' && $_SERVER["PHP_AUTH_PW"] == 'sokol' ) {
    $user = $_SERVER["PHP_AUTH_USER"];
    $pw = $_SERVER["PHP_AUTH_PW"];
    echo "<p>Halo, ovdje $user...</p>\n";
} else {
    http_response_code(401);
    header("WWW-Authenticate: Basic realm=\"Tajni laboratorij Odjela za informatiku\"");
    echo "<p>Moze centrala pogrijesiti jednom ali ne 100 puta!.</p>\n";
}
```

Testiranje s ispravnim loginom:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -u ilijacvorovic:sokol http://localhost:8000/*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'ilijacvorovic'
> GET / HTTP/1.1
> Host: localhost:8000
> Authorization: Basic aWxpamFjdm9yb3ZpYzpzb2tvbA==
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:24:06 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
< Content-type: text/html; charset=UTF-8
< 
<p>Halo, ovdje ilijacvorovic...</p>
* Closing connection 0
```

Testiranje s neispravnim loginom:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -u jakovljevic:sokol http://localhost:8000/
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'jakovljevic'
> GET / HTTP/1.1
> Host: localhost:8000
> Authorization: Basic amFrb3ZsamV2aWM6c29rb2w=
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 401 Unauthorized
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:24:19 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
* Authentication problem. Ignoring this.
< WWW-Authenticate: Basic realm="Tajni laboratorij Odjela za informatiku"
< Content-type: text/html; charset=UTF-8
< 
<p>Moze centrala pogrijesiti jednom ali ne 100 puta!.</p>
* Closing connection 0
```

## Obrada podataka zapisanih u obliku JavaScript Object Notation (JSON) u jeziku PHP

Kako radi json u PHP-u?

```php
<?php

$arr1 = ["moja vrijednost", 1, 3.5, true];
$arr2 = ["moj kljuc" => "moja vrijednost", "broj" => 8, "drugi broj" => 3.5, "je li istina" => true];

$arr3 = $arr2;
$arr3["polje"] = $arr1;
$j3 = json_encode($arr3, JSON_PRETTY_PRINT);
echo $j3;
```

Kako dekodirati json u php-u?

```php
<?php
$j = '{"firstName":"Ivan","lastName":"Horvat","isAlive":true,"age":19,"address":{"streetAddress":"Radmile Matejčić 2a","city":"Rijeka","state":"Primorsko-goranska županija","postalCode":"51000"},"jobTitle":"junior software engineer","phoneNumbers":[{"type":"home","number":"051/999-999"},{"type":"office","number":"099/999-9999"}],"twitterHandle":"@IvanNajjaciHorvat","children":[],"spouse":null}';
$person = json_decode($j, true);
print_r($person);
```

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ php index.php 
Array
(
    [firstName] => Ivan
    [lastName] => Horvat
    [isAlive] => 1
    [age] => 19
    [address] => Array
        (
            [streetAddress] => Radmile Matejčić 2a
            [city] => Rijeka
            [state] => Primorsko-goranska županija
            [postalCode] => 51000
        )

    [jobTitle] => junior software engineer
    [phoneNumbers] => Array
        (
            [0] => Array
                (
                    [type] => home
                    [number] => 051/999-999
                )

            [1] => Array
                (
                    [type] => office
                    [number] => 099/999-9999
                )

        )

    [twitterHandle] => @IvanNajjaciHorvat
    [children] => Array
        (
        )

    [spouse] => 
)
```

No, to nije "čitki" json.. zato koristimo jq:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ echo '{"firstName":"Ivan","lastName":"Horvat","isAlive":true,"age":19,"address":{"streetAddress":"Radmile Matejčić 2a","city":"Rijeka","state":"Primorsko-goranska županija","postalCode":"51000"},"jobTitle":"junior software engineer","phoneNumbers":[{"type":"home","number":"051/999-999"},{"type":"office","number":"099/999-9999"}],"twitterHandle":"@IvanNajjaciHorvat","children":[],"spouse":null}' | jq .
{
  "firstName": "Ivan",
  "lastName": "Horvat",
  "isAlive": true,
  "age": 19,
  "address": {
    "streetAddress": "Radmile Matejčić 2a",
    "city": "Rijeka",
    "state": "Primorsko-goranska županija",
    "postalCode": "51000"
  },
  "jobTitle": "junior software engineer",
  "phoneNumbers": [
    {
      "type": "home",
      "number": "051/999-999"
    },
    {
      "type": "office",
      "number": "099/999-9999"
    }
  ],
  "twitterHandle": "@IvanNajjaciHorvat",
  "children": [],
  "spouse": null
}
```

## HTTP Kolačići i PHP

[gaseri](gaseri.org)
>HTTP kolačić (engl. HTTP cookie, Wikipedia, više detalja o kolačićima na MDN-u) je maleni dio podataka koji korisnički agent pohranjuje na računalu korisnika kod pregledavanja web sjedišta. Na temelju pohranjenih podataka web sjedište pamti stanje koje je korisnik stvorio svojim pregledavanjem, npr. predmete dodane u košaricu za kupnju, prijavu na zatvoreni dio sjedišta upotrebom određenog korisničkog računa ili tekst prethodnih pretraga arhive audiovizualnih datoteka.

Stvorili smo base64 enkodiranjem neke kolačiće:

```shell
cGxlbmtp em9raQ== dmxhZG8= ZHpv emVsZW5rbw== emJvZw== dmFz bWkgamU= c2t1cA== cGxpbg== cGEgbmlzYW0= Z2FzZXI=
```

Napravimo PHP kod koji će pohraniti svakom "prijavljenom" korisniku neki enkriptirani ID u kolačić:

```php
<?php

$file = "secretCodesOnFBserver.json";
if (file_exists($file)) {
    $j = file_get_contents($file);
    $data = json_decode($j, true);
} else {
    $data = [];
}

$tokeni = ["cGxlbmtp", "em9raQ==", "dmxhZG8=", "ZHpv", "emVsZW5rbw==","emJvZw==","dmFz","bWkgamU=","c2t1cA==", "cGxpbg==","cGEgbmlzYW0=","Z2FzZXI="];

if ($_SERVER["REQUEST_URI"] == "/login") {
    if ($_SERVER["REQUEST_METHOD"] == "GET") {
        if (array_key_exists("user_id", $_COOKIE) && array_key_exists($_COOKIE["user_id"], $data)) {
            $user_id = $_COOKIE["user_id"];
            $prev_token = $data[$user_id];
            echo "<p>It is I, " . $prev_token . ".</p>\n";
        } else {
        http_response_code(404);
        echo "<p>Hmm.. sneaky? Tor?</p>\n";
    }
    } else if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $token_key = array_rand($tokeni);
    $tokeni = $tokeni[$token_key];
        echo "<p>You've been Zucced as" . $tokeni . ".</p>\n";
        if (array_key_exists("user_id", $_COOKIE) && array_key_exists($_COOKIE["user_id"], $data)) {
            $user_id = $_COOKIE["user_id"];
            $data[$user_id] = $tokeni;
        } else {
            $data[] = $tokeni;
            $user_id = array_key_last($data);
            setcookie("user_id", $user_id);
        }
    }
}

$j = json_encode($data);
file_put_contents($file, $j);
```

Testirajmo metodama POST i GET s dvije prazne, različite, datoteke:

```shell
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -c cookies.txt -X POST http://localhost:8000/login
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> POST /login HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:50:00 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
* Added cookie user_id="0" for domain localhost, path /, expire 0
< Set-Cookie: user_id=0
< Content-type: text/html; charset=UTF-8
< 
<p>You've been Zucced asem9raQ==.</p>
* Closing connection 0
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ ls
cookies.txt  index.php  secretCodesOnFBserver.json
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ cat cookies.txt 
# Netscape HTTP Cookie File
# https://curl.se/docs/http-cookies.html
# This file was generated by libcurl! Edit at your own risk.

localhost       FALSE   /       FALSE   0       user_id 0

lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -b cookies.txt http://localhost:8000/login
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /login HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
> Cookie: user_id=0
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:50:23 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
< Content-type: text/html; charset=UTF-8
< 
<p>It is I, em9raQ==.</p>
* Closing connection 0
lljubojevic@IdeaPad5-Pro:~/Desktop/public$ curl -v -b cookies.txt -X POST http://localhost:8000/login
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> POST /login HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
> Cookie: user_id=0
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:50:40 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
< Content-type: text/html; charset=UTF-8
< 
<p>You've been Zucced asbWkgamU=.</p>
* Closing connection 0

lljubojevic@IdeaPad5-Pro:~/Desktop/public$  curl -v -c cookies-new.txt -X POST http://localhost:8000/login
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> POST /login HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Host: localhost:8000
< Date: Mon, 07 Nov 2022 18:51:32 GMT
< Connection: close
< X-Powered-By: PHP/8.1.2-1ubuntu2.6
* Added cookie user_id="1" for domain localhost, path /, expire 0
< Set-Cookie: user_id=1
< Content-type: text/html; charset=UTF-8
< 
<p>You've been Zucced asZHpv.</p>
* Closing connection 0
```

## Podmreže

> TODO

## Prevođenje mrežnih adresa - NAT

Ako promotrite ovaj simbolični prikaz "arhitekture" interneta.. sigurno si postavljate pitanje kako je moguće da dvije podmreže na svijetu imaju istu IP adresu. To se kosi s onim što ste do sada naučili.

![NAT - primjer](https://i.postimg.cc/HkwcVkN5/image0.jpg)

Odgovor na to pitanje je nešto što se naziva NAT (Network Address Translation).

> Network Address Translation (NAT) is a service that enables private IP networks to use the internet and cloud. NAT translates private IP addresses in an internal network to a public IP address before packets are sent to an external network.

Kako NAT radi?
> Network Address Translation (NAT) is a service that operates on a router or edge platform to connect private networks to public networks like the internet. NAT is often implemented at the WAN edge router to enable internet access in core, campus, branch, and colocation sites. </br>With NAT, an organization needs one IP address or one limited public IP address to represent an entire group of devices as they connect outside their network. Port Address Translation (PAT) enables one single IP to be shared by multiple hosts using IP and port address translation.

![NAT](https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fwww.mustbegeek.com%2Fwp-content%2Fuploads%2F2018%2F04%2FConfigure-Dynamic-NAT-in-Cisco-IOS-Router.png&f=1&nofb=1&ipt=e3568bf3f451f4075efaac04a305c517052a4af166c25698646d7043c6df85b6&ipo=images)

Vrste NAT-a:

* Statički NAT
    > Static NAT is a private IP address that is a single unregistered IP that is mapped with a legal Public IP address. Here one to one mapping is made within local and global address which is generally applied for web hosting.
* Dinamički NAT
    > Dynamic NAT is an unregistered IP address that is private is changed to a registered public address from a group of the public IP address. If the IP address group is occupied, then the packets are transmitted with a fixed number of the private IP address that can be transmitted to the public address
* PAT (Port Address Translation)
    > Port Address Translation is called NAT overload, where many private IP addresses can be transmitted into unit registered IP addresses. The port numbers are applied to differentiate the traffic flow that belongs to an individual IP address. This is frequently used as a cost-saving method since thousands of servers can be connected to the internet by one real global public IP address.

Zašto se NAT koristi?

* Štedi IP adrese
* Čuva pouzdanost i fleksibilnost Interneta
* NAT ima posebne metode adresiranja mreže
* Dodaje razinu sigurnosti na mrežu jer se uređajima ne može direktno pristupiti

A što je onda CGNAT?
> CGNAT is a network address translation technique that extends the IPv4 networks on a considerable scale and allows ISPs (internet service providers) to conserve their acquired IPv4 pool.
> Every online user has two IP addresses, a public and a private one. When a user intends to communicate online, the standard NAT protocol translates their private IPv4 address to a public one.
> But with CGNAT (LSN or NAT 444), an extra layer of address translation is added. The unique private IP addresses are translated into public IPs shared by multiple users. And this is how ISPs prevent their IPv4 pool from exhaustion.

Prikaz IPv4 NAT pravila na sustavu:

```shell
lljubojevic@IdeaPad-5-Pro:~$ sudo iptables -t nat -L -n -v
[sudo] password for lljubojevic:         
Chain PREROUTING (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain POSTROUTING (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination  
```

NAT tablica preslikavanja:
![NAT routing table](http://2.bp.blogspot.com/-HzVVp33E6mQ/Vpi2AEEtJII/AAAAAAAAG2c/hOTURmdfduI/s1600/NAT-table-test.jpg)

Zašto nam je tu bitan iptables:
[iptables - gaseri](https://gaseri.org/hr/nastava/materijali/iptables-prevodjenje-adresa/#nacin-prevoenja-adresa-alatom-iptables)
[iptables example](https://www.ithands-on.com/2021/09/linux-101-ip-tables-and-nat-network.html)

Definiranje NAT-a na ruteru:

* Uvijek uzmite sučelje na van (za POSTROUTING)

```shell
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

Kako to napraviti kada želimo omogućiti pristup nekom čvoru iz vanjske mreže:

* Na vanjskom ruteru radimo PREROUTING (na unutra) za sve TCP pakete koji dolaze na port 2223 s odredišta (vanjsko sučelje rutera) na odredište u našoj mreži IP:2223

```shell
iptables -t nat -A PREROUTING -p tcp --dport 2223 -d 10.72.0.1 -j DNAT --to-destination 192.168.6.21:2223
```

A kako napraviti pristup svim vratima putem vanjske adrese rutera?

* Na vanjskom ruteru (rubnom u podmreži) radimo POSTROUTING (na van) svega što dolazi s rutera na neki čvor

```shell
iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 10.72.0.1 --to-source 192.168.6.22
```

Vatrozid
> Vatrozid u općenitom smislu podrazumijeva uređaj koji nadzire, te na temelju zadanih pravila, propušta ili odbacuje mrežni promet
> Vatrozid promet filtrira na više slojeva, ovisno o namjeni i potrebi (linkto: tcp/ip stack). Najčešće se promet filtrira na IP i transportnom (TCP, UDP ...) sloju, međutim moguće je definirati pravila filtriranja i za niži – sloj podatkovne poveznice (Ethernet ...) i za viši – aplikacijski sloj (HTTP, SMTP ...).</br> Složenost samog filtriranja ovisi o sloju filtriranja, pa tako filtriranje na višim slojevima omogućuje „pametnije“ filtriranje (viši slojevi sadrže više meta-podataka koji omogućuju selektivnije filtriranje), dok je s druge strane postupak filtriranja na nižim slojevima brži.

Detalji na [gaserima](https://gaseri.org/hr/nastava/materijali/iptables-vatrozid/#laboratorijska-vjezba-3)

* iptables [-t table] {-A|-C|-D} chain rule-specification

Na primjer ako želimo postaviti vatrozid t.d. s nekih mreža možemo pristupati nekim vratima, a s ostalih ne možemo kao u zadatku:

* Postavite iptables na n1, n2 tako da n5, n6, n7 mogu na n17, n18, n19 pristupati samo vratima 8080, 80 i 8443, a da je pristup ostalim vratima zabranjen.

Koristit ćemo slijedeće naredbe:

```shell
n1: 
    iptables -A INPUT -p tcp -s 172.22.0.2/24 --dport 8080 -m state --state NEW,ESTABLISHED -j ACCEPT
    iptables -A INPUT -p tcp -s 172.22.0.2/24 --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
    iptables -A INPUT -p tcp -s 172.22.0.2/24 --dport 8443 -m state --state NEW,ESTABLISHED -j ACCEPT 
   
    
n2: 
   iptables -A INPUT -p tcp -s 172.22.1.20/24 --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
   iptables -A INPUT -p tcp -s 172.22.1.21/24 --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
   iptables -A INPUT -p tcp -s 172.22.1.22/24 --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
   
   iptables -A INPUT -p tcp -s 172.22.1.20/24 --dport 8080 -m state --state NEW,ESTABLISHED -j ACCEPT
   iptables -A INPUT -p tcp -s 172.22.1.21/24 --dport 8080 -m state --state NEW,ESTABLISHED -j ACCEPT
   iptables -A INPUT -p tcp -s 172.22.1.22/24 --dport 8080 -m state --state NEW,ESTABLISHED -j ACCEPT
   
   iptables -A INPUT -p tcp -s 172.22.1.20/24 --dport 8443 -m state --state NEW,ESTABLISHED -j ACCEPT
   iptables -A INPUT -p tcp -s 172.22.1.21/24 --dport 8443 -m state --state NEW,ESTABLISHED -j ACCEPT
   iptables -A INPUT -p tcp -s 172.22.1.22/24 --dport 8443 -m state --state NEW,ESTABLISHED -j ACCEPT
```
