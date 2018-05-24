---
title: Samouczek — wdrażanie stosu LEMP na maszynie wirtualnej z systemem Linux na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak zainstalować stos LEMP na maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 1f4c74ab96fc9494d1b3c7dd626265516533330b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Samouczek: instalowanie serwera internetowego LEMP na maszynie wirtualnej z systemem Linux na platformie Azure

W tym artykule przedstawiono kroki wdrażania serwera internetowego NGINX oraz oprogramowania MySQL i PHP (stosu LEMP) na maszynie wirtualnej z systemem Ubuntu na platformie Azure. Stos LEMP, który stanowi alternatywę dla popularnego [stosu LAMP](tutorial-lamp-stack.md), również można zainstalować na platformie Azure. Aby zobaczyć, jak działa serwer LEMP, możesz opcjonalnie zainstalować i skonfigurować witrynę WordPress. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu (oznaczonym literą „L” w stosie LEMP)
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania NGINX, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na serwerze LEMP

Ta konfiguracja umożliwia szybkie przeprowadzenie testów lub weryfikacji koncepcji.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Instalowanie oprogramowania NGINX, MySQL i PHP

Uruchom następujące polecenie, aby zaktualizować źródła pakietów systemu Ubuntu oraz zainstalować oprogramowanie NGINX, MySQL i PHP. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

Pojawi się monit o zainstalowanie pakietów i innych zależności. Po wyświetleniu monitu ustaw hasło główne programu MySQL, a następnie naciśnij klawisz [Enter], aby kontynuować. Postępuj zgodnie z pozostałymi instrukcjami. W tym procesie jest instalowana minimalna liczba wymaganych rozszerzeń PHP potrzebnych do używania języka PHP z oprogramowaniem MySQL. 

![Strona hasła głównego MySQL][1]

## <a name="verify-installation-and-configuration"></a>Weryfikowanie instalacji i konfiguracji


### <a name="nginx"></a>NGINX

Sprawdź wersję oprogramowania NGINX przy użyciu następującego polecenia:
```bash
nginx -v
```

Po zainstalowaniu serwera NGINX i otwarciu portu 80 dla maszyny wirtualnej można uzyskać dostęp do serwera internetowego z Internetu. Aby wyświetlić stronę powitalną oprogramowania NGINX, otwórz przeglądarkę internetową i wpisz publiczny adres IP maszyny wirtualnej. Wpisz publiczny adres IP użyty do nawiązania połączenia SSH z maszyną wirtualną:

![Strona domyślna serwera NGINX][3]


### <a name="mysql"></a>MySQL

Sprawdź wersję oprogramowania MySQL przy użyciu następującego polecenia (zwróć uwagę na parametr `V` oznaczony wielką literą):

```bash
mysql -V
```

Aby ułatwić ochronę instalacji oprogramowania MySQL, uruchom skrypt `mysql_secure_installation`. Jeśli konfigurujesz tylko serwer tymczasowy, możesz pominąć ten krok. 

```bash
mysql_secure_installation
```

Wprowadź hasło główne oprogramowania MySQL, a następnie skonfiguruj ustawienia zabezpieczeń dla środowiska.

Jeśli chcesz wypróbować funkcje oprogramowania MySQL (na przykład utworzyć bazę danych MySQL, dodać użytkowników lub zmienić ustawienia konfiguracji), zaloguj się do systemu MySQL. Ten krok nie jest wymagany do ukończenia samouczka. 


```bash
mysql -u root -p
```

Gdy skończysz, zamknij wiersz polecenia mysql, wpisując `\q`.

### <a name="php"></a>PHP

Sprawdź wersję oprogramowania PHP przy użyciu następującego polecenia:

```bash
php -v
```

Skonfiguruj serwer NGINX pod kątem używania menedżera procesów FastCGI języka PHP (PHP-FPM). Uruchom następujące polecenia, aby utworzyć kopię zapasową oryginalnego pliku konfiguracji bloku serwera NGINX, a następnie zmodyfikuj oryginalny plik w wybranym edytorze:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

W edytorze zastąp zawartość pliku `/etc/nginx/sites-available/default` następującym kodem. Komentarze zawierają opisy ustawień. Wartość *yourPublicIPAddress* zastąp publicznym adresem IP swojej maszyny wirtualnej, a resztę ustawień pozostaw bez zmian. Następnie zapisz plik.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

Sprawdź konfigurację serwera NGINX pod kątem błędów składniowych:

```bash
sudo nginx -t
```

Jeśli składnia jest poprawna, uruchom ponownie serwer NGINX za pomocą następującego polecenia:

```bash
sudo service nginx restart
```

Jeśli chcesz wykonać dodatkowe testy, utwórz prostą stronę z informacjami o PHP, przeznaczoną do wyświetlania w przeglądarce. Poniższe polecenie tworzy stronę z informacjami o języku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Możesz teraz sprawdzić zawartość utworzonej strony z informacjami o języku PHP. Otwórz przeglądarkę i przejdź pod adres `http://yourPublicIPAddress/info.php`. Zastąp publiczny adres IP maszyny wirtualnej. Zawartość okna powinna wyglądać mniej więcej tak.

![Strona z informacjami o języku PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer LEMP na platformie Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania NGINX, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na stosie LEMP

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwery internetowe przy użyciu certyfikatów SSL.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera internetowego przy użyciu protokołu SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
