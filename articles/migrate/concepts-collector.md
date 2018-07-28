---
title: Urządzenie modułu zbierającego w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie urządzenia modułu zbierającego i sposobu ich konfigurowania.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: c99d0f74dbb8cc28cabebae60fe10645f4bdb3b6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308463"
---
# <a name="collector-appliance"></a>Urządzenie modułu zbierającego

[Usługa Azure Migrate](migrate-overview.md) ocenia obciążenia lokalne pod kątem migracji na platformę Azure. Ten artykuł zawiera informacje o sposobie używania urządzenia modułu zbierającego.



## <a name="overview"></a>Przegląd

W usłudze Azure Migrate Collector to urządzenie uproszczone, który może służyć do wykrywania w lokalnym środowisku vCenter. To urządzenie umożliwia odnalezienie lokalnych maszyn VMware i wysyłania dotyczących ich metadanych do usługi Azure Migrate.

Urządzenie modułu zbierającego jest pakietu OVF, który można pobrać z usługi Azure Migrate projektu. Metoda tworzy maszynę wirtualną VMware z 4 rdzenie, 8 GB pamięci RAM i jeden dysk 80 GB. System operacyjny urządzenia jest system Windows Server 2012 R2 (64-bitowy).

Można utworzyć modułu zbierającego, wykonując kroki opisane w tym miejscu - [sposobu tworzenia maszyny Wirtualnej modułu zbierającego](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Diagram komunikacji modułu zbierającego

![Diagram komunikacji modułu zbierającego](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Składnik      | Element docelowy komunikacji   | Wymagany port                            | Przyczyna                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Moduł zbierający      | Usługa Azure Migrate | TCP 443                                  | Moduł zbierający powinien mieć możliwość komunikacji z usługą za pośrednictwem portu SSL 443 |
| Moduł zbierający      | Program vCenter Server        | Domyślnie 443                             | Moduł zbierający powinno być możliwe do komunikacji z serwerem vCenter. Domyślnie nawiąże połączenie z vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, ten port powinno być dostępne jako port wychodzący w module zbierającym |
| Moduł zbierający      | RDP|   | TCP 3389 | Aby uzyskać aby można było nawiązać połączenie RDP z maszyną modułu zbierającego |





## <a name="collector-pre-requisites"></a>Wymagania wstępne modułu zbierającego

Moduł zbierający musi przekazać kilka wstępnych kontroli w celu zapewnienia można połączyć się z usługą Azure Migrate i przekaż odnalezione dane. Ten artykuł będzie wyglądać na każdym z wymagań wstępnych i zrozumieć, dlaczego jest wymagane.

### <a name="internet-connectivity"></a>Łączność z Internetem

Urządzenie modułu zbierającego musi być połączona z Internetem w celu wysyłania informacji odnalezionych maszyn. Połączenie z komputera z Internetem, w jednym z dwóch sposobów.

1. Można skonfigurować moduł zbierający aby mieć bezpośrednie połączenie z Internetem.
2. Można skonfigurować moduł zbierający Aby połączyć za pośrednictwem serwera proxy.
    * Jeśli serwer proxy wymaga uwierzytelnienia, można określić nazwę użytkownika i hasło w ustawieniach połączenia.
    * Adres IP/nazwę FQDN serwera Proxy, powinien mieć postać http://IPaddress lub http://FQDN. Obsługiwany jest tylko serwer proxy http.

> [!NOTE]
> Serwery proxy oparty na protokole HTTPS nie są obsługiwane przez moduł zbierający.

#### <a name="whitelisting-urls-for-internet-connection"></a>Adresy URL umieszczania na białej liście połączenie z Internetem

Wstępnie wymagane jest pomyślne, jeśli moduł zbierający można połączyć z Internetem przy użyciu podanych ustawień. Sprawdzenie łączności jest weryfikowana przez nawiązanie połączenia listę adresów URL, jak pokazano w poniższej tabeli. Jeśli używane są wszystkie opartego na adresach URL serwera proxy zapory do sterowania ruchem wychodzącym, upewnij się, do listy dozwolonych tych wymaganych adresów URL:

**Adres URL** | **Cel**  
--- | ---
*.portal.azure.com | Wymagany do sprawdzania łączności z usługą platformy Azure i weryfikowanie synchronizacji czasu problemy.

Ponadto wyboru także próbuje zweryfikować łączności z następującymi adresami URL, ale nie zostanie wyboru, jeśli nie jest dostępny. Konfigurowanie listy dozwolonych adresów dla następujących adresów URL jest opcjonalne, ale które należy wykonać ręczne wykonanie czynności, aby uniknąć sprawdzanie wymaganego wstępnie.

**Adres URL** | **Cel**  | **Co zrobić, jeśli nie jest dozwolonych**
--- | --- | ---
*.oneget.org:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | Instalowanie interfejsu PowerCLI nie powiedzie się. Ręcznie Zainstaluj moduł.
*.windows.net:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | Instalowanie interfejsu PowerCLI nie powiedzie się. Ręcznie Zainstaluj moduł.
*.windowsazure.com:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | Instalowanie interfejsu PowerCLI nie powiedzie się. Ręcznie Zainstaluj moduł.
*. powershellgallery.com:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | Instalowanie interfejsu PowerCLI nie powiedzie się. Ręcznie Zainstaluj moduł.
*.msecnd.net:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | Instalowanie interfejsu PowerCLI nie powiedzie się. Ręcznie Zainstaluj moduł.
*.visualstudio.com:443 | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter. | Instalowanie interfejsu PowerCLI nie powiedzie się. Ręcznie Zainstaluj moduł.

### <a name="time-is-in-sync-with-the-internet-server"></a>Czas jest zsynchronizowany z serwerem internetowym

Moduł powinien być zsynchronizowany z internetowym serwerem czasu w celu zapewnienia, że żądania do usługi są uwierzytelniane. Portal.azure.com adres url powinien być dostępny z modułem zbierającym, dzięki czemu można zweryfikować czasu. Jeśli komputer jest zsynchronizowana, musisz zmienić czas zegara na maszynie Wirtualnej modułu zbierającego, aby dopasować bieżący czas, w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie Wirtualnej.
1. Aby sprawdzić strefę czasową, uruchom w32tm /tz.
1. Aby zsynchronizować czas, należy uruchomić w32tm/resync.

### <a name="collector-service-should-be-running"></a>Powinna być uruchomiona usługa modułu zbierającego

Usługa Azure Migrate Collector powinny działać na maszynie. Ta usługa jest uruchamiana automatycznie podczas rozruchu maszyny. Jeśli usługa nie jest uruchomiona, można uruchomić *Azure Migrate Collector* usługi za pośrednictwem Panelu sterowania. Usługa modułu zbierającego odpowiada do łączenia się z serwerem vCenter, zbierać dane wydajności i metadanych maszyn i wysyłać je do usługi.

### <a name="vmware-powercli-65"></a>Program VMware PowerCLI 6.5

Moduł powershell programu VMware PowerCLI należy zainstalować, aby moduł zbierający może komunikować się z serwerem vCenter i wykonywania zapytań o szczegóły maszyny i dane wydajności. Moduł programu powershell jest automatycznie pobierany i instalowany jako część wyboru wstępnych. Automatyczne pobieranie wymaga kilku adresy URL na liście dozwolonych, kończy się niepowodzeniem, której potrzebujesz albo podaj przez umieszczania na białej liście uzyskiwać do nich dostęp, lub ręcznego instalowania modułu.

Zainstaluj moduł ręcznie wykonując następujące czynności:

1. Aby zainstalować interfejs PowerCli w module zbierającym bez połączenia internetowego, wykonaj kroki podane w [ten link](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. Po zainstalowaniu modułu programu PowerShell na innym komputerze, który ma dostęp do Internetu, skopiuj pliki VMware.* na tej maszynie, do komputera modułu zbierającego.
3. Uruchom ponownie sprawdzanie wymagań wstępnych i upewnij się, że interfejs PowerCLI został zainstalowany.

## <a name="connecting-to-vcenter-server"></a>Łączenie z programem vCenter Server

Moduł zbierający, należy nawiązać połączenie z serwerem vCenter i mieć możliwość wykonywania zapytań dotyczących maszyn wirtualnych, ich metadanych i ich licznikami wydajności. Te dane są używane przez projekt, aby obliczyć ocenę.

1. Aby połączyć z programem vCenter Server, tylko do odczytu konta z uprawnieniami, jak pokazano w poniższej tabeli może służyć do uruchamiania odnajdywania.

    |Zadanie  |Wymagana rola/konto  |Uprawnienia  |
    |---------|---------|---------|
    |Odnajdywanie urządzenia na podstawie modułów zbierających dane    | Potrzebujesz co najmniej użytkownik tylko do odczytu        |Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu         |

2. Tylko tych centrów danych, które są dostępne na określone konto vCenter jest możliwy do odnajdywania.
3. Należy określić serwer vCenter w pełni kwalifikowaną nazwę domeny/adres IP, aby połączyć się z serwerem vCenter. Domyślnie zostanie nawiązane za pośrednictwem portu 443. Jeśli skonfigurowano serwer vCenter do nasłuchiwania na inny numer portu, możesz je określić jako część adresu serwera w formie IPAddress:Port_Number lub FQDN:Port_Number.
4. Ustawienia statystyk programu vCenter Server powinna być równa poziom 3, przed rozpoczęciem wdrażania. Jeśli poziom jest niższy niż 3, odnajdywania zostanie ukończone, ale nie będą zbierane dane wydajności dotyczące magazynu i sieci. Zalecenia dotyczące rozmiaru oceny w tym przypadku będzie zależeć od dane wydajności dotyczące procesora CPU i pamięci, a tylko dane konfiguracji dla dysku i kart sieciowych. [Dowiedz się więcej](./concepts-collector.md) na jakie dane są zbierane i jak wpływa na ocenę.
5. Moduł powinien mieć sieci linii wzroku do serwera vCenter.

> [!NOTE]
> Tylko vCenter Server 5.5, 6.0 i 6.5 są oficjalnie obsługiwane wersje.

> [!IMPORTANT]
> Zalecane ustawienie najwyższy poziom wspólnych (3) do poziomu statystyk, tak, aby wszystkie liczniki są poprawnie zbierane. Jeśli masz ustawiony na niższym poziomie vCenter, tylko kilka liczniki mogą być zbierane w całkowicie, z użyciem usług rest ustawione na 0. Ocena może następnie wyświetlać niepełne dane.

### <a name="selecting-the-scope-for-discovery"></a>Wybierając zakres odnajdowania

Po nawiązaniu połączenia, aby serwer vCenter, można wybrać zakres odnajdywania. Wybranie zakresu umożliwia odnalezienie wszystkich maszyn wirtualnych ze ścieżki magazynu określony vCenter.

1. Zakres może być centrum danych, folder lub hoście ESXi.
2. Jednocześnie można wybrać tylko jeden zakres. Aby wybrać więcej maszyn wirtualnych, można ukończyć jedne operacje odnajdywania i ponownie uruchom proces wykrywania przy użyciu nowego zakresu.
3. Można wybrać tylko zakres, który ma *mniej niż 1500 maszyn wirtualnych*.

## <a name="specify-migration-project"></a>Określanie projektu migracji

Po lokalnym programie vCenter jest połączony i zakresu jest określony, można teraz określić szczegóły projektu migracji, które będzie używane do odnajdywania i oceny. Określ identyfikator i klucz projektu, a następnie połącz.

## <a name="start-discovery-and-view-collection-progress"></a>Rozpocznij odnajdowanie i Wyświetl postęp zbierania

Po uruchomieniu odnajdywania, maszyny wirtualne vCenter są odnajdywane i ich metadanych i wydajności dane są wysyłane do serwera. Stan postępu informuje o następujących identyfikatorów:

1. Identyfikator modułu zbierającego: Unikatowy identyfikator, który znajduje się na komputerze modułu zbierającego. Ten identyfikator nie zmienia się na danym komputerze przez inne wykrycia. Możesz użyć tego Identyfikatora w przypadku awarii podczas zgłaszania problemu do firmy Microsoft Support.
2. Identyfikator sesji: Unikatowy identyfikator uruchomionego zadania zbierania. Po zakończeniu zadania odnajdywania mogą odwoływać się do tego samego Identyfikatora sesji w portalu. Ten identyfikator zmienia się dla każdego zadania odzyskiwania. W razie awarii możesz zgłosić tego Identyfikatora do firmy Microsoft Support.

### <a name="what-data-is-collected"></a>Jakie dane są zbierane?

Zadania odzyskiwania umożliwia odnalezienie następujących statyczne metadane dotyczące wybranych maszyn wirtualnych.

1. Nazwa wyświetlana maszyny Wirtualnej (na klawiaturze vCenter)
2. Ścieżka magazynu maszyny Wirtualnej (w programie vCenter hosta/folder)
3. Adres IP
4. Adres MAC
5. System operacyjny
5. Liczba rdzeni, dysków, kart sieciowych
6. Rozmiar pamięci, rozmiary dysków
7. Liczniki wydajności i maszyny Wirtualnej, dysku i sieci, zgodnie z opisem w poniższej tabeli.

Poniższa tabela zawiera listę liczników wydajności, które są zbierane i zawiera również listę wyników oceny, które ma wpływ, jeśli nie ma określonego licznika.

|Licznik                                  |Poziom    |Poziom na urządzenie  |Ocena wpływu                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |Nie dotyczy                |Zalecany rozmiar maszyny Wirtualnej i kosztów                    |
|mem.usage.average                        | 1       |Nie dotyczy                |Zalecany rozmiar maszyny Wirtualnej i kosztów                    |
|virtualDisk.read.average                 | 2       |2                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|virtualDisk.write.average                | 2       |2                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|NET.RECEIVED.AVERAGE                     | 2       |3                 |Koszt rozmiar i sieć maszyny Wirtualnej                        |
|net.transmitted.average                  | 2       |3                 |Koszt rozmiar i sieć maszyny Wirtualnej                        |

> [!WARNING]
> Jeśli właśnie zostały ustawione na wyższy poziom statystyk, potrwa na dzień wygenerowania liczników wydajności. Dlatego zaleca się, uruchom odnajdywanie po jednym dniu.

### <a name="time-required-to-complete-the-collection"></a>Czas wymagany do ukończenia kolekcji

Moduł zbierający tylko umożliwia odnalezienie danych komputera i wysyła je do projektu. Projekt może zająć dodatkowy czas, zanim odnalezione dane jest wyświetlany w portalu i możesz rozpocząć tworzenie oceny.

Na podstawie liczby maszyn wirtualnych w wybranym zakresie, trwa maksymalnie 15 minut do wysyłania metadanych statycznej do projektu. Po statyczne metadanych jest dostępna w portalu, można zapoznać się z listą maszyn w portalu i zacznij tworzyć grupy. Nie można utworzyć oceny, do momentu ukończenia zadania odzyskiwania i projektu zostało przetworzone dane. Gdy zadanie kolekcji zostało ukończone w module zbierającym, może potrwać maksymalnie jedną godzinę, dane wydajności, które mogą być dostępne w witrynie portal, na podstawie liczby maszyn wirtualnych w wybranym zakresie.

## <a name="locking-down-the-collector-appliance"></a>Blokowanie urządzenia modułu zbierającego
Firma Microsoft zaleca systemem ciągłych aktualizacji Windows urządzenia modułu zbierającego. Jeśli moduł zbierający nie zostaną zaktualizowane przez 60 dni, moduł zbierający rozpocznie zamykanie maszyny automatycznie. Jeśli działa odnajdywanie, maszyny zostaną nie wyłączone, nawet jeśli jest on poza jego 60-dniowego okresu. Zadanie odnajdowania po zakończeniu maszyny zostaną wyłączone. Jeśli używasz modułu zbierającego ponad 45 dniach, zaleca się pozostawienie maszyny zaktualizować cały czas uruchomione Windows Update.

Zalecamy również następujące kroki, aby zabezpieczyć urządzenie
1. Nie można udostępniać lub ze strony nieautoryzowanych zostanie zgubiony przez użytkownika hasła administratora.
2. Zamknij urządzenie nieużywane.
3. Umieść urządzenie w zabezpieczonej sieci.
4. Po zakończeniu pracy migracji, należy usunąć wystąpienia urządzenia. Należy również usunąć dysk, wykonywanie kopii zapasowych plików (Vmdk), ponieważ dysków może mieć vCenter poświadczenia buforowane na nich.

## <a name="how-to-upgrade-collector"></a>Jak uaktualnić moduł zbierający

Moduł zbierający można uaktualnić do najnowszej wersji, bez pobierania OVA jeszcze raz.

1. Pobierz najnowszy [pakiet uaktualniający](https://aka.ms/migrate/col/upgrade_9_13) (wersja 1.0.9.13).
2. Aby upewnij się, że poprawka pobrany bezpieczne, Otwórz okno wiersza polecenia administratora i uruchom następujące polecenie, aby wygenerować wartość skrótu dla pliku ZIP. Wygenerowany skrót powinien odpowiadać za pomocą skrótu wymienionych dla określonej wersji:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (przykład użycia C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.7.zip SHA256)
3. Skopiuj plik zip do usługi Azure Migrate collector maszyny wirtualnej (urządzenia modułu zbierającego).
4. Kliknij prawym przyciskiem myszy w pliku zip, a następnie wybierz Wyodrębnij wszystkie.
5. Kliknij prawym przyciskiem myszy na Setup.ps1 i wybierz polecenie Uruchom przy użyciu programu PowerShell, a następnie postępuj zgodnie z instrukcjami na ekranie, aby zainstalować aktualizację.

### <a name="list-of-updates"></a>Lista aktualizacji

#### <a name="upgrade-to-version-10913"></a>Uaktualnienie do wersji 1.0.9.13

Wartości dla uaktualnienie mieszania [pakietu 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>Uaktualnienie do wersji 1.0.9.11

Wartości dla uaktualnienie mieszania [pakietu 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Uaktualnienie do wersji 1.0.9.7

Wartości dla uaktualnienie mieszania [pakietu 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie oceny dla maszyn wirtualnych VMware w środowisku lokalnym](tutorial-assessment-vmware.md)
