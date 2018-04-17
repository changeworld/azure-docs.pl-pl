---
title: Wdrażanie stosu LAMP na maszynie wirtualnej z systemem Linux na platformie Azure | Microsoft Docs
description: Samouczek — instalowanie stosu oprogramowania LAMP na maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 21790a44ff60bd11202814efd5c0f32e8b614ec4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Instalowanie serwera internetowego LAMP na maszynie wirtualnej platformy Azure
W tym artykule przedstawiono kroki wdrażania serwera internetowego Apache oraz oprogramowania MySQL i PHP (stosu LAMP) na maszynie wirtualnej z systemem Ubuntu na platformie Azure. Jeśli wolisz używać serwera internetowego NGINX, zobacz samouczek dotyczący [stosu LEMP](tutorial-lemp-stack.md). Aby zobaczyć, jak działa serwer LAMP, możesz opcjonalnie zainstalować i skonfigurować witrynę WordPress. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu (oznaczonym literą „L” w stosie LAMP)
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania Apache, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na serwerze LAMP


Ta konfiguracja umożliwia szybkie przeprowadzenie testów lub weryfikacji koncepcji. Aby uzyskać więcej informacji na temat stosu LAMP, w tym zalecenia dotyczące środowiska produkcyjnego, zobacz [dokumentację systemu Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalowanie oprogramowania Apache, MySQL i PHP

Uruchom następujące polecenie, aby zaktualizować źródła pakietów systemu Ubuntu oraz zainstalować oprogramowanie Apache, MySQL i PHP. Widoczny na końcu polecenia znak karetki (^) jest częścią nazwy pakietu `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```


Pojawi się monit o zainstalowanie pakietów i innych zależności. Po wyświetleniu monitu ustaw hasło główne programu MySQL, a następnie naciśnij klawisz [Enter], aby kontynuować. Postępuj zgodnie z pozostałymi instrukcjami. W tym procesie jest instalowana minimalna liczba wymaganych rozszerzeń PHP potrzebnych do używania języka PHP z oprogramowaniem MySQL. 

![Strona hasła głównego MySQL][1]

## <a name="verify-installation-and-configuration"></a>Weryfikowanie instalacji i konfiguracji


### <a name="apache"></a>Apache

Sprawdź wersję oprogramowania Apache przy użyciu następującego polecenia:
```bash
apache2 -v
```

Po zainstalowaniu oprogramowania Apache i otwarciu portu 80 dla maszyny wirtualnej można uzyskać dostęp do serwera internetowego z Internetu. Aby wyświetlić stronę domyślną oprogramowania Apache2 Ubuntu, otwórz przeglądarkę internetową i wpisz publiczny adres IP maszyny wirtualnej. Wpisz publiczny adres IP użyty do nawiązania połączenia SSH z maszyną wirtualną:

![Strona domyślna oprogramowania Apache][3]


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

Jeśli chcesz wykonać dodatkowe testy, utwórz prostą stronę z informacjami o PHP, przeznaczoną do wyświetlania w przeglądarce. Poniższe polecenie tworzy stronę z informacjami o języku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Możesz teraz sprawdzić zawartość utworzonej strony z informacjami o języku PHP. Otwórz przeglądarkę i przejdź pod adres `http://yourPublicIPAddress/info.php`. Zastąp publiczny adres IP maszyny wirtualnej. Zawartość okna powinna wyglądać mniej więcej tak.

![Strona z informacjami o języku PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer LAMP na platformie Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania Apache, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na serwerze LAMP

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwery internetowe przy użyciu certyfikatów SSL.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera internetowego przy użyciu protokołu SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png