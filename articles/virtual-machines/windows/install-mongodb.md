---
title: Instalowanie bazy danych MongoDB Windows maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować bazy danych MongoDB na maszynie Wirtualnej platformy Azure, system Windows Server 2012 R2 utworzone za pomocą modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: f3fe9751467a1fc34f4e9d02855c4aff307424a3
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2017
ms.locfileid: "26745983"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalowanie i Konfigurowanie bazy danych MongoDB na maszynie Wirtualnej systemu Windows na platformie Azure
[Bazy danych MongoDB](http://www.mongodb.org) jest popularnych open source, wysokiej wydajności bazę danych NoSQL. W tym artykule opisano sposób instalowania i konfigurowania bazy danych MongoDB na maszynie wirtualnej systemu Windows Server 2016 (VM) na platformie Azure. Możesz również [zainstalować bazy danych MongoDB na Maszynę wirtualną systemu Linux na platformie Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainstalowaniem i skonfigurowaniem bazy danych MongoDB, należy utworzyć Maszynę wirtualną, a w idealnym przypadku dodania dysku danych do niego. Zobacz następujące artykuły, aby utworzyć Maszynę wirtualną i Dodaj dysk danych:

* Tworzenie maszyny Wirtualnej systemu Windows Server przy użyciu [portalu Azure](quick-create-portal.md) lub [programu Azure PowerShell](quick-create-powershell.md).
* Dołączenie dysku danych do maszyny Wirtualnej systemu Windows Server za pomocą [portalu Azure](attach-managed-disk-portal.md) lub [programu Azure PowerShell](attach-disk-ps.md).

Aby rozpocząć instalowanie i Konfigurowanie bazy danych MongoDB, [Zaloguj się do maszyny Wirtualnej systemu Windows Server](connect-logon.md) przy użyciu pulpitu zdalnego.

## <a name="install-mongodb"></a>Instalowanie bazy danych MongoDB
> [!IMPORTANT]
> Funkcje zabezpieczeń bazy danych MongoDB, takich jak uwierzytelnianie i powiązanie adresu IP, nie są domyślnie włączone. Funkcje zabezpieczeń powinny być włączone przed wdrożeniem w środowisku produkcyjnym bazy danych MongoDB. Aby uzyskać więcej informacji, zobacz [MongoDB zabezpieczeń i uwierzytelniania](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego, Otwórz program Internet Explorer z poziomu paska zadań.
2. Wybierz **Użyj zalecanych ustawień zabezpieczeń, prywatności i zgodności** gdy program Internet Explorer najpierw otwiera i kliknij przycisk **OK**.
3. Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest domyślnie włączona. Dodaj witrynę bazy danych MongoDB do listy dozwolonych witryn:
   
   * Wybierz **narzędzia** ikonę w prawym górnym rogu.
   * W **Opcje internetowe**, wybierz pozycję **zabezpieczeń** , a następnie wybierz **Zaufane witryny** ikony.
   * Kliknij przycisk **witryny** przycisku. Dodaj *https://\*. mongodb.com* do listy zaufanych witryn, a następnie zamknij okno dialogowe.
     
     ![Konfigurowanie ustawień zabezpieczeń programu Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Przejdź do [MongoDB — pliki do pobrania](http://www.mongodb.com/downloads) strony (http://www.mongodb.com/downloads).
5. W razie potrzeby wybierz **Community Server** wersji, a następnie wybierz, najnowsza stabilna Bieżące wydanie*systemu Windows Server 2008 R2 w wersji 64-bitowej lub nowszym*. Aby pobrać Instalatora, kliknij przycisk **pobierania (msi)**.
   
    ![Pobierz Instalator bazy danych MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Po zakończeniu pobierania, należy uruchomić Instalatora.
6. Przeczytaj i zaakceptuj umowę licencyjną. Po wyświetleniu monitu wybierz **Complete** zainstalować.
7. W razie potrzeby można również zainstalować kompas, interfejsu graficznego bazy danych mongodb.
8. Na ekranie końcowym, kliknij przycisk **zainstalować**.

## <a name="configure-the-vm-and-mongodb"></a>Skonfiguruj maszynę Wirtualną i bazy danych MongoDB
1. Zmienne ścieżek nie są aktualizowane przez Instalatora bazy danych MongoDB. Bez MongoDB `bin` lokalizacji w zmiennej path, należy określić pełną ścieżkę każdym użyciu wykonywalny bazy danych MongoDB. Aby dodać lokalizację do zmiennej path:
   
   * Kliknij prawym przyciskiem myszy **Start** menu, a następnie wybierz **systemu**.
   * Kliknij przycisk **Zaawansowane ustawienia systemu**, a następnie kliknij przycisk **zmiennych środowiskowych**.
   * W obszarze **zmienne systemowe**, wybierz pozycję **ścieżki**, a następnie kliknij przycisk **Edytuj**.
     
     ![Skonfiguruj zmienne ŚCIEŻEK](./media/install-mongodb/configure-path-variables.png)
     
     Dodaj ścieżkę do Twojej bazy danych MongoDB `bin` folderu. Bazy danych MongoDB zazwyczaj jest zainstalowany w *C:\Program Files\MongoDB*. Sprawdź ścieżkę instalacji na maszynie Wirtualnej. W poniższym przykładzie dodano domyślnej lokalizacji do instalacji bazy danych MongoDB `PATH` zmienną:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Pamiętaj dodać wiodącego średnika (`;`) wskazująca, czy dodajesz lokalizację do Twojej `PATH` zmiennej.

2. Tworzenie bazy danych MongoDB katalogi danych i dziennika na dysku danych. Z **Start** menu, wybierz opcję **wiersza polecenia**. Poniższe przykłady tworzenia katalogów na dysku F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Uruchom wystąpienie bazy danych MongoDB przy użyciu następującego polecenia dostosowywania ścieżka do danych i dziennika odpowiednio katalogi:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Może upłynąć kilka minut dla bazy danych MongoDB przydzielić plików dziennika i rozpocząć nasłuchiwania dla połączeń. Wszystkie komunikaty dziennika są kierowane do *F:\MongoLogs\mongolog.log* pliku jako `mongod.exe` server uruchamia i przydziela plików dziennika.
   
   > [!NOTE]
   > Wiersz polecenia pozostaje koncentruje się na to zadanie jest uruchomiona wystąpienia bazy danych MongoDB. Zamykaj okna wiersza polecenia do kontynuowania pracy bazy danych MongoDB. Ewentualnie można zainstalować bazy danych MongoDB jako usługa, zgodnie z opisem w następnym kroku.

4. W przypadku bardziej niezawodne środowisko bazy danych MongoDB, zainstaluj `mongod.exe` jako usługa. Tworzenie usługi oznacza, że nie ma potrzeby pozostaw wiersz polecenia uruchamianiu za każdym razem, którego chcesz użyć bazy danych MongoDB. Utwórz usługę następujące odpowiednio dostosowywania ścieżka do danych i dziennika katalogów:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Poprzednie polecenie tworzy usługę o nazwie bazy danych MongoDB, opis "BD o Mongo". Są także określić następujące parametry:
   
   * `--dbpath` Opcji określa lokalizację katalogu danych.
   * `--logpath` Opcji należy używać do określenia pliku dziennika, ponieważ uruchomiona usługa nie ma okno polecenia, aby wyświetlić dane wyjściowe.
   * `--logappend` Opcja określa, czy ponowne uruchomienie usługi powoduje, że dane wyjściowe do dołączenia do istniejącego pliku dziennika.
   
   Aby uruchomić usługę bazy danych MongoDB, uruchom następujące polecenie:
   
    ```
    net start MongoDB
    ```
   
    Aby uzyskać więcej informacji na temat tworzenia usługi dla bazy danych MongoDB, zobacz [skonfigurować usługę systemu Windows dla bazy danych MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testowe wystąpienie bazy danych MongoDB
Z bazy danych MongoDB, uruchomione jako jedno wystąpienie lub zainstalowany jako usługa można teraz rozpocząć tworzenie i korzystanie z baz danych. Aby uruchomić powłoki administracyjne bazy danych MongoDB, otworzyć inne okno wiersza polecenia z **Start** menu, a następnie wprowadź następujące polecenie:

```
mongo  
```

Możesz wyświetlić listę z bazami danych z `db` polecenia. Wstaw dowolne dane w następujący sposób:

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

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurowanie zapory i reguł sieciowej grupy zabezpieczeń
Teraz, bazy danych MongoDB jest zainstalowana i uruchomiona, należy otworzyć port w Zaporze systemu Windows, możesz zdalnie nawiązać połączenie bazy danych MongoDB. Aby utworzyć nową regułę ruchu przychodzącego zezwalająca na porcie TCP 27017, otwórz administracyjny wiersz środowiska PowerShell i wpisz następujące polecenie:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Można również utworzyć regułę przy użyciu **Zapora systemu Windows z zabezpieczeniami zaawansowanymi** graficzne narzędzie do zarządzania. Utwórz nową regułę ruchu przychodzącego zezwalająca na porcie TCP 27017.

W razie potrzeby utwórz zasadę sieciową grupę zabezpieczeń, aby zezwolić na dostęp do bazy danych MongoDB z poza istniejącą podsieć sieci wirtualnej platformy Azure. Można utworzyć reguły grupy zabezpieczeń sieci przy użyciu [portalu Azure](nsg-quickstart-portal.md) lub [programu Azure PowerShell](nsg-quickstart-powershell.md). Podobnie jak w przypadku reguł zapory systemu Windows umożliwiają port TCP 27017 do interfejsu sieci wirtualnej maszyny wirtualnej bazy danych MongoDB.

> [!NOTE]
> TCP port 27017 jest domyślny port używany przez bazy danych MongoDB. Tego portu można zmienić za pomocą `--port` parametru podczas uruchamiania `mongod.exe` ręcznie lub z poziomu usługi. Jeśli zmienisz numer portu, upewnij się, można zaktualizować reguł zapory systemu Windows i grupy zabezpieczeń sieci w poprzednich krokach.


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób instalowania i konfigurowania bazy danych MongoDB na maszynie Wirtualnej systemu Windows. Na maszynie Wirtualnej systemu Windows, można uzyskać dostęp bazy danych MongoDB, wykonując następujące tematy Zaawansowane w [dokumentacją usługi MongoDB](https://docs.mongodb.com/manual/).

