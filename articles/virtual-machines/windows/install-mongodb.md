---
title: Instalowanie bazy danych MongoDB na Windows maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować bazę danych MongoDB na maszynie Wirtualnej platformy Azure z systemem Windows Server 2012 R2 przy użyciu modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: afd8e6b47fb86985acde062af1fb38ec3af4e902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711450"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalowanie i Konfigurowanie bazy danych MongoDB na maszynie Wirtualnej Windows na platformie Azure
[Bazy danych MongoDB](https://www.mongodb.org) to popularne typu open source, wysokiej wydajności baza danych NoSQL. Ten artykuł przeprowadzi Cię przez proces instalowania i konfigurowania bazy danych MongoDB na maszynie wirtualnej systemu Windows Server 2016 (VM) na platformie Azure. Możesz również [zainstalować bazę danych MongoDB na maszynie Wirtualnej systemu Linux na platformie Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainstalowaniem i Konfigurowanie bazy danych MongoDB, musisz utworzyć Maszynę wirtualną, a w idealnym przypadku dodania dysku danych do niego. Zobacz następujące artykuły, aby utworzyć Maszynę wirtualną i Dodaj dysk danych:

* Tworzenie maszyny Wirtualnej z systemem Windows Server przy użyciu [witryny Azure portal](quick-create-portal.md) lub [programu Azure PowerShell](quick-create-powershell.md).
* Dołączanie dysku danych do maszyny Wirtualnej z systemem Windows Server za pomocą [witryny Azure portal](attach-managed-disk-portal.md) lub [programu Azure PowerShell](attach-disk-ps.md).

Aby rozpocząć instalowanie i Konfigurowanie bazy danych MongoDB, [Zaloguj się do maszyny Wirtualnej z systemem Windows Server](connect-logon.md) przy użyciu pulpitu zdalnego.

## <a name="install-mongodb"></a>Instalowanie bazy danych MongoDB
> [!IMPORTANT]
> Funkcje zabezpieczeń bazy danych MongoDB, takie jak uwierzytelnianie i powiązanie adresu IP, nie są domyślnie włączone. Funkcje zabezpieczeń powinien być włączony przed wdrożeniem usługi MongoDB w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [zabezpieczeń bazy danych MongoDB i uwierzytelniania](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego, Otwórz program Internet Explorer z paska zadań.
2. Wybierz **Użyj zalecanych ustawień zabezpieczeń, ochrony prywatności i zgodności** po programu Internet Explorer najpierw zostanie otwarty i kliknij przycisk **OK**.
3. Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona domyślnie. Dodaj witrynę sieci Web bazy danych MongoDB do listy dozwolonych witryn:
   
   * Wybierz **narzędzia** ikonę w prawym górnym rogu.
   * W **Opcje internetowe**, wybierz opcję **zabezpieczeń** , a następnie wybierz pozycję **Zaufane witryny** ikony.
   * Kliknij przycisk **witryn** przycisku. Dodaj *https://\*. mongodb.com* do listy zaufanych witryn, a następnie zamknij okno dialogowe.
     
     ![Konfiguruj ustawienia zabezpieczeń programu Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Przejdź do [MongoDB — pliki do pobrania](https://www.mongodb.com/downloads) strony (https://www.mongodb.com/downloads).
5. Jeśli to konieczne, zaznacz **Community Server** wersji, a następnie wybierz pozycję, najnowsza wersja stabilna bieżące wersji*systemu Windows Server 2008 R2 64-bitowych lub nowszy*. Aby pobrać Instalatora, kliknij przycisk **pobierania (msi)**.
   
    ![Pobierz Instalator bazy danych MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Po zakończeniu pobierania, uruchom Instalatora.
6. Przeczytaj i zaakceptuj umowę licencyjną. Po wyświetleniu monitu wybierz **Complete** instalacji.
7. Jeśli to konieczne, można także zainstalować Compass, interfejs graficzny dla bazy danych MongoDB.
8. Na ostatnim ekranie kliknij **zainstalować**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurowanie maszyny Wirtualnej i bazy danych MongoDB
1. Zmienne ścieżek nie są aktualizowane przez Instalatora bazy danych MongoDB. Bez bazy danych MongoDB `bin` lokalizacji w zmiennej path, należy określić pełną ścieżkę w każdym użyciu wykonywalnej bazy danych MongoDB. Aby dodać lokalizację do zmiennej path:
   
   * Kliknij prawym przyciskiem myszy **Start** menu, a następnie wybierz **systemu**.
   * Kliknij przycisk **Zaawansowane ustawienia systemu**, a następnie kliknij przycisk **zmienne środowiskowe**.
   * W obszarze **zmiennych systemowych**, wybierz opcję **ścieżki**, a następnie kliknij przycisk **Edytuj**.
     
     ![Skonfiguruj zmienne ŚCIEŻEK](./media/install-mongodb/configure-path-variables.png)
     
     Dodaj ścieżkę do swojej bazy danych MongoDB `bin` folderu. Bazy danych MongoDB jest zwykle instalowany w *C:\Program Files\MongoDB*. Sprawdź, czy ścieżka instalacji na maszynie Wirtualnej. W poniższym przykładzie dodano domyślnej lokalizacji, do instalacji bazy danych MongoDB `PATH` zmiennej:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Pamiętaj dodać wiodącego średnika (`;`) do wskazania, czy dodajesz lokalizację w której Twoje `PATH` zmiennej.

2. Tworzenie katalogów danych i dziennika bazy danych MongoDB na dysku danych. Z **Start** menu, wybierz opcję **polecenia**. Poniższe przykłady tworzą katalogi na napęd F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Uruchom wystąpienie bazy danych MongoDB za pomocą następującego polecenia, dostosowując ścieżka do danych i dziennika odpowiednio katalogi:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Może upłynąć kilka minut, zanim bazy danych MongoDB do przydzielania plików dziennika i rozpocząć nasłuchiwania połączeń. Wszystkie komunikaty dziennika są kierowane do *F:\MongoLogs\mongolog.log* plik jako `mongod.exe` server rozpoczyna się i przydziela plików dziennika.
   
   > [!NOTE]
   > Wiersz polecenia pozostaje koncentruje się na to zadanie, gdy wystąpienie bazy danych MongoDB jest uruchomiona. Pozostaw okno wiersza polecenia otwarte, aby kontynuować uruchamianie bazy danych MongoDB. Lub Zainstaluj bazę danych MongoDB jako usługa, zgodnie z opisem w następnym kroku.

4. Bardziej niezawodne środowisko pracy bazy danych MongoDB, należy zainstalować `mongod.exe` jako usługa. Tworzenie usługi oznacza, że nie potrzebujesz pozostawić uruchamianiu za każdym razem, którego chcesz użyć bazy danych MongoDB wiersza polecenia. Tworzenie usługi w następujący sposób, w związku z tym Dopasowywanie ścieżki do katalogów danych i dziennika:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Poprzednie polecenie tworzy usługi o nazwie bazy danych MongoDB, opis "Mongo DB". Są również określić następujące parametry:
   
   * `--dbpath` Opcja określa lokalizację katalogu danych.
   * `--logpath` Opcji należy używać do określenia pliku dziennika, ponieważ uruchomionej usługi nie ma okno polecenia, aby wyświetlić dane wyjściowe.
   * `--logappend` Opcja określa, że ponowne uruchomienie usługi powoduje, że dane wyjściowe dołączyć do istniejącego pliku dziennika.
   
   Aby uruchomić usługę bazy danych MongoDB, uruchom następujące polecenie:
   
    ```
    net start MongoDB
    ```
   
    Aby uzyskać więcej informacji na temat tworzenia usługi bazy danych MongoDB, zobacz [skonfigurować usługę Windows dla programu MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testowe wystąpienie bazy danych MongoDB
Bazy danych MongoDB, uruchomione jako pojedyncze wystąpienie lub zainstalowany jako usługa można teraz rozpocząć tworzenie i korzystanie z baz danych. Aby uruchomić administracyjne powłoki bazy danych MongoDB, Otwórz inne okno wiersza polecenia z **Start** menu, a następnie wprowadź następujące polecenie:

```
mongo  
```

Możesz wyświetlić listę baz danych przy użyciu `db` polecenia. Wstaw dowolne dane w następujący sposób:

```
db.foo.insert( { a : 1 } )
```

Wyszukiwanie danych w następujący sposób:

```
db.foo.find()
```

Dane wyjściowe są podobne do poniższego przykładu:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Zakończ `mongo` konsoli w następujący sposób:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurowanie zapory i reguły sieciowej grupy zabezpieczeń
Teraz, bazy danych MongoDB jest zainstalowana i uruchomiona, należy otworzyć port w Zaporze Windows zdalnie do połączenia się z bazą danych MongoDB. Aby utworzyć nową regułę ruchu przychodzącego zezwalająca na porcie TCP 27017, otwórz administracyjny wiersz polecenia PowerShell i wpisz następujące polecenie:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Można również utworzyć reguły, używając **Zapora Windows z zabezpieczeniami zaawansowanymi** graficzne narzędzie do zarządzania. Utwórz nową regułę ruchu przychodzącego zezwalająca na porcie TCP 27017.

Jeśli to konieczne, Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwalać na dostęp do bazy danych MongoDB z poza istniejącej podsieci sieci wirtualnej platformy Azure. Reguły sieciowej grupy zabezpieczeń można utworzyć przy użyciu [witryny Azure portal](nsg-quickstart-portal.md) lub [programu Azure PowerShell](nsg-quickstart-powershell.md). Podobnie jak w przypadku zasad zapory Windows Zezwalaj na porcie TCP 27017 do interfejsu sieci wirtualnej maszyny wirtualnej bazy danych MongoDB.

> [!NOTE]
> TCP port 27017 jest domyślnym portem używanym przez bazy danych MongoDB. Ten port można zmienić za pomocą `--port` parametru podczas uruchamiania `mongod.exe` ręcznie lub przy użyciu usługi. Jeśli zmienisz numer portu, upewnij się zaktualizować reguły zapory Windows i sieciowej grupy zabezpieczeń, w poprzednich krokach.


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób instalowania i konfigurowania bazy danych MongoDB na maszynie Wirtualnej Windows. Możesz teraz uzyskiwać dostęp bazy danych MongoDB na maszynie Wirtualnej Windows, wykonując poniższe tematy Zaawansowane w [dokumentacją usługi MongoDB](https://docs.mongodb.com/manual/).

