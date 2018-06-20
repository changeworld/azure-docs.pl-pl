---
title: Moduł zbierający urządzenia w migracji Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie urządzenia moduł zbierający i sposobie konfigurowania go.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: d0f36370f5093f8c1d06c83a62532b3854597fa4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211673"
---
# <a name="collector-appliance"></a>Moduł zbierający urządzenia

[Azure migracji](migrate-overview.md) ocenia obciążeń lokalnych do migracji do usługi Azure. Ten artykuł zawiera informacje o sposobie używania urządzenia modułu zbierającego.



## <a name="overview"></a>Przegląd

Moduł zbierający migracji Azure to lekkie urządzenia, który może służyć do wykrywania w lokalnym środowisku vCenter. Urządzenie umożliwia odnalezienie lokalnych maszyn VMware i wysyła metadane dotyczące ich do usługi Azure migracji.

Urządzenia moduł zbierający jest OVF pobieranych z Azure migracji projektu. Metoda tworzy maszynę wirtualną VMware o 4 rdzenie, 8 GB pamięci RAM i jeden dysk 80 GB. System operacyjny urządzenia jest Windows Server 2012 R2 (64-bitowe).

Można utworzyć modułu zbierającego, wykonując kroki opisane w tym miejscu - [sposób utworzyć maszynę Wirtualną modułu zbierającego](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Moduł zbierający komunikacji diagramu

![Moduł zbierający komunikacji diagramu](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Składnik      | Element docelowy komunikacji   | Wymagany port                            | Przyczyna                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Moduł zbierający      | Usługa Azure Migrate | TCP 443                                  | Moduł zbierający powinny mieć możliwość komunikacji z usługą za pośrednictwem portu SSL 443 |
| Moduł zbierający      | Program vCenter Server        | Domyślnie 443                             | Moduł zbierający powinno być możliwe do komunikacji z serwerem vCenter. Łączy się vCenter na 443 domyślnie. Jeśli vCenter nasłuchuje na innym porcie, ten port powinny być dostępne jako port wychodzący modułu zbierającego |
| Moduł zbierający      | RDP|   | TCP 3389 | Można mieć możliwość RDP do komputera modułu zbierającego |





## <a name="collector-pre-requisites"></a>Moduł zbierający wymagania wstępne

Moduł zbierający musi przekazać kilka kontroli wstępnych w celu zapewnienia można połączyć się z usługą Azure migracji i przekazywanie odnalezionych danych. Ten artykuł wygląda na każdym z wymagań wstępnych i zrozumienie, dlaczego jest wymagane.

### <a name="internet-connectivity"></a>Łączność z Internetem

Moduł zbierający urządzenia musi być połączony z Internetem, aby wysłać informacje o wykrytych komputerach. Komputer można połączyć się z Internetem w jednym z dwóch sposobów.

1. Istnieje możliwość skonfigurowania modułu zbierającego mieć bezpośrednie połączenie z Internetem.
2. Istnieje możliwość skonfigurowania modułu zbierającego połączenia za pomocą serwera proxy.
    * Jeśli serwer proxy wymaga uwierzytelnienia, można określić nazwę użytkownika i hasło w ustawieniach połączenia.
    * Adres IP/FQDN serwera Proxy, powinien mieć postać http://IPaddress lub http://FQDN. Obsługiwane jest tylko serwer proxy http.

> [!NOTE]
> Serwery proxy oparty na protokole HTTPS nie są obsługiwane przez moduł zbierający.

#### <a name="whitelisting-urls-for-internet-connection"></a>Adresy URL listę dozwolonych podobnej połączenia internetowego

Sprawdzanie wymaganego wstępnie zakończy się pomyślnie, jeśli moduł zbierający można połączyć się z Internetem przy użyciu podanych ustawień. Sprawdź łączność jest zweryfikowany przez nawiązanie połączenia listę adresów URL, jak podano w poniższej tabeli. Jeśli używasz dowolnego zapora oparta na adres URL serwera proxy do sterowania łączność wychodząca, upewnij się listą dozwolonych adresów IP są wymagane adresów URL:

**Adres URL** | **Cel**  
--- | ---
*.portal.azure.com | Wymagane sprawdzenie połączenia z usługą Azure i sprawdzić poprawności synchronizacji czasu problemy.

Ponadto sprawdzania także próbuje zweryfikować łączności o następujących adresach URL, ale nie zostanie sprawdzania, jeśli nie jest dostępna. Konfigurowanie listy dozwolonych adresów IP dla następujących adresów URL jest opcjonalne, ale należy wykonać w celu złagodzenia sprawdzanie wymaganego wstępnie wymagane ręczne wykonanie czynności.

**Adres URL** | **Cel**  | **Co zrobić, jeśli nie jest dozwolone**
--- | --- | ---
*.oneget.org:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | PowerCLI instalacja nie powiedzie się. Ręcznie Zainstaluj moduł.
*.windows.net:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | PowerCLI instalacja nie powiedzie się. Ręcznie Zainstaluj moduł.
*.windowsazure.com:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | PowerCLI instalacja nie powiedzie się. Ręcznie Zainstaluj moduł.
*. powershellgallery.com:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | PowerCLI instalacja nie powiedzie się. Ręcznie Zainstaluj moduł.
*.msecnd.net:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | PowerCLI instalacja nie powiedzie się. Ręcznie Zainstaluj moduł.
*.visualstudio.com:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | PowerCLI instalacja nie powiedzie się. Ręcznie Zainstaluj moduł.

### <a name="time-is-in-sync-with-the-internet-server"></a>Czas jest zsynchronizowana z serwera internetowego

Moduł zbierający powinny być zsynchronizowane z serwerem czasu internet, aby upewnić się, że są uwierzytelniane żądań do usługi. Portal.azure.com adres url powinien być dostępny z modułu zbierającego, dzięki czemu można zweryfikować czasu. Jeśli komputer nie jest zsynchronizowana, musisz zmienić czas zegara na maszynie Wirtualnej modułu zbierającego, aby dopasować bieżący czas, w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie Wirtualnej.
1. Aby sprawdzić strefę czasową, uruchom w32tm /tz.
1. Aby zsynchronizować czas, należy uruchomić w32tm/resync.

### <a name="collector-service-should-be-running"></a>Powinna być uruchomiona usługa modułu zbierającego

Usługa modułu zbierającego Azure migracji powinna działać na tym komputerze. Ta usługa jest uruchomiona automatycznie po rozruchu maszyny. Jeśli usługa nie działa, możesz uruchomić *modułu zbierającego Azure migracji* usługi za pośrednictwem Panelu sterowania. Usługa modułu zbierającego jest odpowiedzialny do łączenia się z serwerem vCenter, gromadzenia danych wydajności i metadanych maszyny i wysyłania go do usługi.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

Moduł powershell VMware PowerCLI należy zainstalować, aby moduł zbierający może komunikować się z serwerem vCenter i zapytanie o szczegóły maszyny i ich dane dotyczące wydajności. Moduł programu powershell jest automatycznie pobierane i instalowane jako część wyboru wstępnych. Automatyczne pobieranie wymaga kilku białej adresów URL, w przeciwnym muszą albo dostarczać przez listę dozwolonych podobnej do nich dostęp, lub ręcznie zainstalować moduł.

Zainstaluj moduł ręcznie, wykonując następujące czynności:

1. Aby zainstalować PowerCli modułu zbierającego bez połączenia internetowego, wykonaj kroki podane w [to łącze](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. Po zainstalowaniu modułu programu PowerShell na innym komputerze, który ma dostęp do Internetu, należy skopiować pliki VMware.* z tego komputera do komputera modułu zbierającego.
3. Uruchom ponownie sprawdzanie wymagań wstępnych i upewnij się, że zainstalowano PowerCLI.

## <a name="connecting-to-vcenter-server"></a>Łączenie z serwerem vCenter

Moduł zbierający należy nawiązać połączenia z serwerem vCenter i móc wysyłać zapytania dotyczące maszyn wirtualnych, ich metadanych i ich liczników wydajności. Te dane są używane przez projekt do obliczania oceny.

1. Aby połączyć z serwerem vCenter, tylko do odczytu konta z uprawnieniami, jak podano w poniższej tabeli można służyć do uruchamiania odnajdywania.

    |Zadanie  |Wymagana Rola/konta  |Uprawnienia  |
    |---------|---------|---------|
    |Odnajdywanie urządzenia na podstawie modułów zbierających dane    | Należy co najmniej użytkownika tylko do odczytu        |Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu         |

2. Tylko tych centrach danych, które są dostępne na koncie vCenter jest możliwy do odnajdywania.
3. Należy określić nazwę FQDN lub adres IP do łączenia się z serwerem vCenter vCenter. Domyślnie będzie się łączyć za pośrednictwem portu 443. Jeśli skonfigurowano vCenter do nasłuchiwania na inny numer portu, można określić go jako część adresu serwera w formie IPAddress:Port_Number lub FQDN:Port_Number.
4. Ustawienia statystyk dla serwera vCenter należy wybrać poziom 3 przed rozpoczęciem wdrażania. Jeśli poziom jest niższy niż 3, odnajdowanie zostanie ukończona, ale nie można zebrać danych wydajności magazynu i sieci. Zalecenia dotyczące rozmiaru oceny w takim przypadku będzie opierać się na dane wydajności dotyczące Procesora i pamięci, a tylko dane konfiguracyjne dla kart dysku i sieci. [Dowiedz się więcej](./concepts-collector.md) na jakie dane są zbierane i jak wpływa na oceny.
5. Moduł zbierający ma procesów w wierszu sieci, z serwerem vCenter.

> [!NOTE]
> Tylko vCenter Server w wersji 5.5, 6.0 i 6.5 oficjalnie są obsługiwane.

> [!IMPORTANT]
> Zalecane ustawienie najwyższego poziomu wspólnych (3) dla poziomu statystyk, tak aby wszystkie liczniki są poprawnie zbierane. Jeśli masz vCenter ustawiony na niższym poziomie tylko kilka liczniki mogą być zbierane całkowicie z resztą równa 0. Ocena następnie mogą być wyświetlane niekompletne dane.

### <a name="selecting-the-scope-for-discovery"></a>Wybieranie zakresu odnajdowania

Po nawiązaniu połączenia vCenter, można wybrać zakres odnajdywania. Wybieranie zakresu odnajduje wszystkie maszyny wirtualne z vCenter określona ścieżka spisu.

1. Zakres może być centrum danych, folder lub hosta ESXi.
2. Jednocześnie można wybrać tylko jeden zakres. Aby wybrać więcej maszyn wirtualnych, można zakończyć jeden odnajdywania i ponownego uruchomienia procesu odnajdywania z nowego zakresu.
3. Można wybrać tylko zakres, który ma *mniej niż 1500 maszyn wirtualnych*.

## <a name="specify-migration-project"></a>Określanie projektu migracji

Po lokalnymi vCenter jest połączony i zakres jest określony, można teraz określić szczegółami projektu migracji, które będzie używane do odnajdywania i oceny. Określ klucz i identyfikator projektu i połącz.

## <a name="start-discovery-and-view-collection-progress"></a>Uruchom postępu kolekcji odnajdowania i widoku

Po uruchomieniu odnajdywania program vCenter maszyny wirtualne są wykrywane, a ich metadanych i wydajności dane są wysyłane do serwera. Stan postępu informuje o następujących identyfikatorów:

1. Identyfikator modułu zbierającego: Unikatowy identyfikator, który znajduje się na komputerze modułu zbierającego. Ten identyfikator nie zmienia dla danego komputera przez inne wykrycia. Za pomocą tego Identyfikatora, w przypadku niepowodzenia podczas raportowania problem do firmy Microsoft Support.
2. Identyfikator sesji: Unikatowy identyfikator dla wykonywanym zadaniem kolekcji. Po zakończeniu zadania odnajdywania mogą odwoływać się do tego samego Identyfikatora sesji w portalu. Ten identyfikator zmian dla każdego zadania kolekcji. W przypadku awarii ten identyfikator może raportować do firmy Microsoft Support.

### <a name="what-data-is-collected"></a>Jakie dane są zbierane?

Zadanie kolekcji umożliwia odnalezienie następujących statycznych metadane dotyczące wybranych maszyn wirtualnych.

1. Nazwa wyświetlana maszyny Wirtualnej (w programie vCenter)
2. Ścieżka spisu maszyny Wirtualnej (w programie vCenter hosta/folder)
3. Adres IP
4. Adres MAC
5. System operacyjny
5. Liczba rdzeni, dysków, kart sieciowych
6. Rozmiar pamięci, rozmiarów dysków
7. Liczniki wydajności i maszyny Wirtualnej, dysku i sieci wymienione w poniższej tabeli.

W poniższej tabeli wymieniono liczniki wydajności, które są zbierane, a także wyświetla wyniki oceny, które ma wpływ, jeśli nie ma określonego licznika.

|Licznik                                  |Poziom    |Poziom na urządzenie  |Ocena wpływu                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |Nie dotyczy                |Zalecany rozmiar maszyny Wirtualnej i kosztów                    |
|mem.usage.average                        | 1       |Nie dotyczy                |Zalecany rozmiar maszyny Wirtualnej i kosztów                    |
|virtualDisk.read.average                 | 2       |2                 |Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej         |
|virtualDisk.write.average                | 2       |2                 |Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej         |
|NET.RECEIVED.AVERAGE                     | 2       |3                 |Koszt rozmiar i sieci maszyny Wirtualnej                        |
|net.transmitted.average                  | 2       |3                 |Koszt rozmiar i sieci maszyny Wirtualnej                        |

> [!WARNING]
> Jeśli właśnie ustawione na wyższy poziom statystyki potrwa na dzień do generowania liczników wydajności. Tak firma Microsoft zaleca uruchomienia odnajdywania po jednym dniu.

### <a name="time-required-to-complete-the-collection"></a>Czas wymagany do ukończenia kolekcji

Moduł zbierający tylko umożliwia odnalezienie danych komputera i wysyła je do projektu. Projekt może zająć dodatkowy czas przed odnalezione dane są wyświetlane w portalu i rozpoczęciem tworzenia ocenę.

Na podstawie liczby maszyn wirtualnych w wybranym zakresie, trwa maksymalnie 15 minut do wysyłania statycznych metadanych do projektu. Po statycznych metadane są dostępne w portalu, możesz zapoznać się z listą maszyn w portalu i rozpocząć tworzenie grupy. Nie można utworzyć oceny, dopiero po zakończeniu zadania zbierania i projektu zostało przetworzone dane. Raz zadanie pobierania zostało ukończone w module zbierającym, może potrwać maksymalnie jedną godzinę dane dotyczące wydajności są dostępne w portalu, zależy od liczby maszyn wirtualnych w wybranym zakresie.

## <a name="locking-down-the-collector-appliance"></a>Blokowanie urządzenia modułu zbierającego
Firma Microsoft zaleca ciągłego aktualizacji systemu Windows na urządzeniu modułu zbierającego. Jeśli moduł zbierający nie zostanie zaktualizowane 45 dni, przez moduł zbierający spowoduje uruchomienie zamykanie maszyny automatycznie. Jeśli działa odnajdywanie, komputer zostanie nie zostać wyłączony, nawet jeśli jest ona poza jego 45 dni. Kończy POST zadanie odnajdywania, maszyny zostaną wyłączone. Jeśli moduł zbierający jest używana do więcej niż 45 dni, zaleca się pozostawienie maszyny zaktualizowane na cały czas, przez uruchomionej usługi Windows update.

Zalecamy również następujące kroki, aby zabezpieczyć urządzenia
1. Udostępnij lub nie misplace hasła administratora z osoby nieupoważnione.
2. Wyłącz urządzenia nieużywane.
3. Umieść urządzenia w zabezpieczonej sieci.
4. Po zakończeniu pracy migracji, Usuń to wystąpienie urządzenia. Należy również usunąć dysku wykonywanie kopii zapasowych plików (VMDKs), jak dyski może mieć poświadczenia vCenter je w pamięci podręcznej.

## <a name="how-to-upgrade-collector"></a>Jak uaktualnić moduł zbierający

Moduł zbierający można uaktualnić do najnowszej wersji, bez pobierania komórki jajowe jeszcze raz.

1. Pobierz najnowszą [pakiet uaktualniający](https://aka.ms/migrate/col/upgrade_9_11) (wersja 1.0.9.11).
2. W celu zapewnienia bezpiecznego pobrany poprawki, Otwórz okno polecenia administratora i uruchom następujące polecenie, aby wygenerować skrótu dla pliku ZIP. Skrót wygenerowanego powinno być zgodne z skrótu wymienionych dla określonej wersji:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (Przykładowe użycie C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.7.zip SHA256)
3. Skopiuj plik zip do migracji Azure modułu zbierającego maszyny wirtualnej (moduł zbierający urządzenia).
4. Kliknij prawym przyciskiem myszy w pliku zip, a następnie wybierz Wyodrębnij wszystkie.
5. Kliknij prawym przyciskiem myszy na Setup.ps1 i wybierz polecenie Uruchom przy użyciu programu PowerShell i postępuj zgodnie z instrukcjami na ekranie, aby zainstalować aktualizację.

### <a name="list-of-updates"></a>Lista aktualizacji

#### <a name="upgrade-to-version-10911"></a>Uaktualnienie do wersji 1.0.9.11

Wartości do uaktualnienia mieszania [pakietu 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Uaktualnienie do wersji 1.0.9.7

Wartości do uaktualnienia mieszania [pakietu 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie oceny dla lokalnych maszyn wirtualnych VMware](tutorial-assessment-vmware.md)
