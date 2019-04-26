---
title: O urządzenia modułu zbierającego w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera informacje dotyczące urządzenia modułu zbierającego w usłudze Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 224511b9748c540f2cd48a3d8393a9c74f76ce32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679292"
---
# <a name="about-the-collector-appliance"></a>O urządzenia modułu zbierającego

 Ten artykuł zawiera informacje o usłudze Azure Migrate Collector.

Azure Migrate Collector to urządzenie uproszczone, który może służyć do wykrywania w lokalnym środowisku vCenter na potrzeby oceny za pomocą [usługi Azure Migrate](migrate-overview.md) usługi, przed migracją na platformę Azure.  

## <a name="discovery-method"></a>Metoda odnajdywania

Wcześniej były dwie opcje urządzenia modułu zbierającego, jednorazowe i ciągłe odnajdywania. Model jednorazowe odnajdywania jest już przestarzały, ponieważ opiera się na ustawieniach statystyk serwera vCenter do zbierania danych wydajności (statystyki wymagane ustawienia należy ustawić poziom 3) i również zbierane średnia liczników (zamiast szczyt), które spowodowało niepełną zmiany rozmiaru. Model ciągłe odnajdywania zapewnia zbierania szczegółowych danych i skutkuje dokładne zmiany rozmiaru z powodu zbiór liczników szczytowego. Poniżej przedstawiono, jak to działa:

Urządzenie modułu zbierającego stale jest podłączony do projektu Azure Migrate i stale zbiera dane dotyczące wydajności maszyn wirtualnych.

- Moduł zbierający stale profilów w środowisku lokalnym na potrzeby zbierania danych użycia w czasie rzeczywistym, co 20 sekund.
- Urządzenie zbiera przykłady 20 sekund i tworzy jeden punkt danych co 15 minut.
- Do tworzenia danych punkt urządzenie szczytowa wartość wybierana jest opcja przykłady 20 sekund i wysyła je do platformy Azure.
- Ten model nie są zależne od ustawienia statystyk serwera vCenter, tak aby zbierać dane dotyczące wydajności.
- Aby zatrzymać, ciągłe profilowania w dowolnym momencie z modułu zbierającego.

**Szybkie oceny:** Przy użyciu urządzenia odnajdywania ciągłe, po zakończeniu odnajdywania (zajmuje kilka godzin w zależności od liczby maszyn wirtualnych), możesz od razu utworzyć oceny. Ponieważ zbieranie danych o wydajności rozpoczyna się wraz z rozpoczęciem odnajdywania, jeśli potrzebujesz szybkich ocen, wybierz w ocenie kryterium ustalania rozmiaru *zgodnie ze środowiskiem lokalnym*. W przypadku ocen na podstawie wydajności zaleca się poczekanie przez co najmniej jeden dzień po rozpoczęciu odnajdywania, aby uzyskać miarodajne zalecenia dotyczące rozmiaru.

Urządzenie ciągle tylko zbiera dane wydajności, nie wykrywa zmiany konfiguracji w środowisku lokalnym, (tj. Dodawanie maszyny Wirtualnej, usuwania, dodawania dysku itp.). W przypadku zmiany konfiguracji w środowisku lokalnym możesz wykonać następujące działania, aby odzwierciedlić zmiany w portalu:

- Dodanie elementów (maszyn wirtualnych, dysków, rdzeni itp.): aby uwzględnić te zmiany w witrynie Azure Portal, możesz zatrzymać odnajdywanie z urządzenia i następnie uruchomić je ponownie. Zapewni to, że zmiany zostaną zaktualizowane w projekcie usługi Azure Migrate.

- Usunięcie maszyn wirtualnych: ze względu na konstrukcję urządzenia, usunięcie maszyny wirtualnej nie zostanie uwzględnione, nawet jeśli zatrzymasz odnajdywanie i uruchomisz je ponownie. Przyczyną jest to, że dane z kolejnych operacji odnajdywania są dołączane do starszych danych, a nie nadpisywane. W takim przypadku możesz po prostu zignorować maszynę wirtualną w portalu, usuwając ją z grupy i obliczając ponownie ocenę.

> [!NOTE]
> Urządzenie jednorazowego odnajdywania jest już przestarzałe, ponieważ ta metoda opierała się na ustawieniach statystyk programu vCenter Server w zakresie dostępności punktów danych wydajności i zbierała średnią liczników wydajności, co powodowało określanie zbyt małego rozmiaru maszyn wirtualnych na potrzeby migracji na platformę Azure.

## <a name="deploying-the-collector"></a>Wdrażanie modułu zbierającego

Możesz wdrożyć urządzenie modułu zbierającego przy użyciu szablonu pakietu OVF:

- Szablon OVF możesz pobrać z usługi Azure Migrate projektu w witrynie Azure portal. Możesz zaimportować pobrany plik programem vCenter Server, aby skonfigurować urządzenie modułu zbierającego maszyny Wirtualnej.
- Z pakietu OVF VMware konfiguruje Maszynę wirtualną z 8 rdzeni, 16 GB pamięci RAM i jeden dysk 80 GB. System operacyjny jest system Windows Server 2016 (64-bitowy).
- Po uruchomieniu modułu zbierającego, uruchom szereg wymagań wstępnych, aby upewnij się, czy moduł zbierający łączy się usługa Azure Migrate.

- [Dowiedz się więcej](tutorial-assessment-vmware.md#create-the-collector-vm) dotyczących tworzenia modułu zbierającego.


## <a name="collector-prerequisites"></a>Wymagania wstępne dotyczące modułu zbierającego

Moduł zbierający musi przekazać kilka Sprawdzanie wymagań wstępnych, aby zapewnić możliwość połączenia z usługi Azure Migrate w Internecie i przekazywanie odnalezionych danych.

- **Sprawdź chmury platformy Azure**: Moduł zbierający musi wiedzieć, w chmurze platformy Azure, do którego jest planowana migracja.
    - Jeśli planujesz migrację do chmury platformy Azure dla instytucji rządowych, wybierz pozycję Azure dla instytucji rządowych.
    - Wybierz Azure Global, jeśli planujesz migrację do chmury komercyjnej platformy Azure.
    - Oparte na chmurze, określone w tym miejscu, urządzenie będzie wysyłać odnalezione metadane do odpowiednich punktów końcowych.
- **Sprawdź połączenie internetowe**: Moduł zbierający można połączyć się z Internetem bezpośrednio lub za pośrednictwem serwera proxy.
    - Sprawdzanie wymagań wstępnych sprawdza łączność z [adresów URL wymaganych i opcjonalnych](#urls-for-connectivity).
    - Jeśli masz bezpośrednie połączenie z Internetem, brak określonej czynności jest wymagany, innym niż upewniając się, że moduł zbierający może osiągnąć wymaganych adresów URL.
    - Jeśli łączysz się za pośrednictwem serwera proxy, należy zwrócić uwagę na poniższe wymagania.
- **Sprawdź synchronizację czasu**: Moduł zbierający powinien być zsynchronizowany z internetowym serwerem czasu w celu zapewnienia, że żądania do usługi są uwierzytelniane.
    - Portal.azure.com adres url powinien być dostępny z modułem zbierającym, dzięki czemu można zweryfikować czasu.
    - Jeśli komputer nie jest zsynchronizowany, musisz zmienić czas zegara na maszynie Wirtualnej modułu zbierającego, aby dopasować bieżący czas. Aby zrobić to otwórz wiersz administratora na maszynie Wirtualnej Uruchom **w32tm /tz** do sprawdzenia strefy czasowej. Uruchom **w32tm/resync** do synchronizacji czasu.
- **Sprawdź uruchomiona usługa modułu zbierającego**:  Usługa Azure Migrate Collector powinny działać na maszynie Wirtualnej modułu zbierającego.
    - Ta usługa jest uruchamiana automatycznie podczas rozruchu maszyny.
    - Jeśli usługa nie jest uruchomiona, należy ją uruchomić z poziomu Panelu sterowania.
    - Usługa modułu zbierającego nawiązanie połączenia z serwerem vCenter, służy do zbierania danych wydajności i metadanych maszyny Wirtualnej i wysyła je do usługi Azure Migrate.
- **Sprawdź VMware PowerCLI 6.5 został zainstalowany**: Moduł PowerShell programu VMware PowerCLI 6.5 musi być zainstalowany na maszynie Wirtualnej modułu zbierającego, tak aby może komunikować się z serwerem vCenter.
    - Jeśli moduł zbierający dostęp do adresów URL, wymagane do zainstalowania modułu, jest on zainstalowany automatycznie podczas wdrażania modułu zbierającego.
    - Jeśli moduł zbierający nie może zainstalować moduł podczas wdrażania, możesz zainstalować go ręcznie.
- **Sprawdź połączenie z programem vCenter Server**: Moduł zbierający musi mieć możliwość programu vCenter Server i zapytanie o maszyny wirtualne, metadane i liczników wydajności. [Sprawdź wymagania wstępne dotyczące](#connect-to-vcenter-server) łączenia.


### <a name="connect-to-the-internet-via-a-proxy"></a>Połącz się z Internetem za pośrednictwem serwera proxy

- Serwer proxy wymaga uwierzytelniania, można określić nazwę użytkownika i hasło, podczas konfigurowania modułu zbierającego.
- Adres IP/nazwę FQDN serwera Proxy, należy określać jako *http:\//IPaddress* lub *http:\//FQDN*.
- Obsługiwane są tylko serwery proxy HTTP. Serwery proxy oparty na protokole HTTPS nie są obsługiwane przez moduł zbierający.
- Jeśli serwer proxy jest przechwytujący serwer proxy, należy zaimportować certyfikat serwera proxy na maszynie wirtualnej modułu zbierającego.
  1. W maszynie Wirtualnej modułu zbierającego, przejdź do **Start Menu** > **zarządzania certyfikatami komputera**.
  2. W narzędziu certyfikaty w ramach **certyfikaty — komputer lokalny**, Znajdź **zaufanych wydawców** > **certyfikaty**.

      ![Narzędzia certyfikatów](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. Skopiuj certyfikat serwera proxy na maszynie Wirtualnej modułu zbierającego. Może być konieczne go uzyskać od administratora sieci.
  4. Kliknij dwukrotnie, aby otworzyć certyfikat, a następnie kliknij przycisk **Zainstaluj certyfikat**.
  5. W Kreatorze importu certyfikatów > Store lokalizacji, wybierz **komputera lokalnego**.

     ![Lokalizacja magazynu certyfikatów](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. Wybierz **Umieść wszystkie certyfikaty w następującym magazynie** > **Przeglądaj** > **zaufanych wydawców**. Kliknij przycisk **Zakończ** Aby zaimportować certyfikat.

     ![Magazyn certyfikatów](./media/concepts-intercepting-proxy/certificate-store.png)

  7. Sprawdź, czy certyfikat został zaimportowany, zgodnie z oczekiwaniami, a następnie sprawdź, czy działa sprawdzania wymagań wstępnych połączenia internetowego jako oczekiwana.


### <a name="urls-for-connectivity"></a>Adresy URL dla połączenia

Sprawdzenie łączności jest weryfikowana przez nawiązanie połączenia listę adresów URL.

**Adres URL** | **Szczegóły**  | **Sprawdzanie wymagań wstępnych**
--- | --- | ---
*.portal.azure.com | Dotyczy globalna platforma Azure. Służy do sprawdzania łączności z usług platformy Azure i synchronizacji czasu. | Dostęp do adres URL jest wymagany.<br/><br/> Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie ma łączności.
*.portal.azure.us | Dotyczy tylko systemu Azure dla instytucji rządowych. Służy do sprawdzania łączności z usług platformy Azure i synchronizacji czasu. | Dostęp do adres URL jest wymagany.<br/><br/> Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie ma łączności.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Umożliwia pobieranie modułu programu PowerShell vCenter PowerCLI. | Wymagany jest dostęp do adresów URL.<br/><br/> Sprawdzanie wymagań wstępnych nie będzie się nie powieść.<br/><br/> Instalacja automatyczna modułu na maszynie Wirtualnej modułu zbierającego nie powiedzie się. Musisz zainstalować moduł ręcznie na maszynie, która ma łączność z Internetem, a następnie skopiuj modułów do urządzenia. [Dowiedz się więcej, przechodząc do kroku nr 4 w tym przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception).


### <a name="install-vmware-powercli-module-manually"></a>Zainstaluj ręcznie program VMware PowerCLI modułu

1. Zainstaluj moduł przy użyciu polecenia [te kroki](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Poniższe kroki opisują instalacji w trybie online i offline.
2. Jeśli maszyna wirtualna modułu zbierającego jest w trybie offline i zainstaluj w module na innym komputerze z dostępem do Internetu, należy skopiować pliki VMware.* z tego komputera z maszyną wirtualną modułu zbierającego.
3. Po zakończeniu instalacji ponownego uruchomienia sprawdzania wymagań wstępnych, aby upewnić się, że interfejs PowerCLI został zainstalowany.

### <a name="connect-to-vcenter-server"></a>Połącz z programem vCenter Server

Moduł zbierający łączy się z serwerem vcenter i wysyła zapytanie dotyczące metadanych maszyny Wirtualnej i liczników wydajności. Oto, co jest potrzebne do połączenia.

- VCenter Server 5.5, 6.0, 6.5 i 6.7 obsługiwane są tylko wersje.
- Potrzebujesz tylko do odczytu konta z uprawnieniami podsumowano poniżej do odnajdywania. Tylko centrów danych dostępne za pomocą konta jest możliwy do odnajdywania.
- Domyślnie połączenie z serwerem vCenter za pomocą nazwy FQDN lub adres IP. Jeśli na oprogramowania vCenter Server nasłuchuje na innym porcie, nawiążesz z nim za pomocą formularza *IPAddress:Port_Number* lub *FQDN:Port_Number*.
- Moduł powinien mieć sieci linii wzroku do serwera vCenter.

#### <a name="account-permissions"></a>Uprawnień konta

**Konto** | **Uprawnienia**
--- | ---
Co najmniej użytkownik tylko do odczytu konta | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu   


## <a name="collector-communications"></a>Moduł zbierający komunikacji

Moduł zbierający komunikuje się zgodnie z opisem w następujących tabeli i diagramu.

![Diagram komunikacji modułu zbierającego](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Moduł zbierający komunikuje się z** | **Port** | **Szczegóły**
--- | --- | ---
Usługa Azure Migrate | TCP 443 | Moduł zbierający komunikuje się z usługą Azure Migrate za pośrednictwem protokołu SSL 443.
Program vCenter Server | TCP 443 | Moduł zbierający musi mieć możliwość komunikacji z serwerem vCenter.<br/><br/> Domyślnie łączy się vCenter na porcie 443.<br/><br/> Jeśli na oprogramowania vCenter Server nasłuchuje na innym porcie, ten port powinny być dostępne jako port wychodzący na modułu zbierającego.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>Zebrano metadane

> [!NOTE]
> Metadanych wykrytych przez usługę Azure Migrate służy urządzenia modułu zbierającego ułatwiające odpowiedniego rozmiaru aplikacji podczas migracji na platformę Azure, wykonywać analizy gotowości platformy Azure, analiza zależności aplikacji i planowanie kosztów. Firma Microsoft używa tych danych w odniesieniu do dowolnego inspekcja zgodności licencji.

Urządzenie modułu zbierającego umożliwia odnalezienie następujących metadanych konfiguracji dla każdej maszyny Wirtualnej. Dane konfiguracji dla maszyn wirtualnych są dostępne godziny po uruchomieniu odnajdywania.

- Nazwa wyświetlana maszyny Wirtualnej (w programie vCenter Server)
- Ścieżka magazynu maszyny Wirtualnej (host/folderu w programie vCenter Server)
- Adres IP
- Adres MAC
- System operacyjny
- Liczba rdzeni, dysków, kart sieciowych
- Rozmiar pamięci, rozmiary dysków
- Liczniki wydajności maszyn wirtualnych, dysku i sieci.

### <a name="performance-counters"></a>Liczniki wydajności

 Urządzenie modułu zbierającego zbiera następujące liczniki wydajności dla każdej maszyny Wirtualnej z hosta ESXi w interwału wynoszącego 20 sekund. Te liczniki są liczniki vCenter i chociaż terminologii mówi średnia próbek 20-sekundowe liczników w czasie rzeczywistym. Dane wydajności dla maszyn wirtualnych zostanie uruchomiony, stają się dostępne w portalu po dwóch godzinach od zostały rozpoczęte odnajdywania. Zdecydowanie zaleca się poczekać co najmniej dzień przed utworzeniem oceny na podstawie wydajności, aby uzyskać dokładne zalecenia dotyczące doboru wielkości. Jeśli szukasz natychmiastowej gratyfikacji, możesz utworzyć oceny przy użyciu kryterium ustalania rozmiaru jako *jako lokalne* zostaną nie będą dane dotyczące wydajności w przypadku ustalania rozmiaru po prawej stronie.

**Counter** |  **Wpływ na ocenę**
--- | ---
cpu.usage.average | Zalecany rozmiar maszyny Wirtualnej i kosztów  
mem.usage.average | Zalecany rozmiar maszyny Wirtualnej i kosztów  
virtualDisk.read.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
virtualDisk.write.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
virtualDisk.numberReadAveraged.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
virtualDisk.numberWriteAveraged.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
net.received.average | Oblicza rozmiar maszyny Wirtualnej                          
net.transmitted.average | Oblicza rozmiar maszyny Wirtualnej     

Pełną listę liczników VMware zebrane przez usługę Azure Migrate jest dostępna poniżej:

**Kategoria** |  **Metadata** | **punkt danych vCenter**
--- | --- | ---
Szczegóły maszyny | Identyfikator maszyny wirtualnej | vm.Config.InstanceUuid
Szczegóły maszyny | Nazwa maszyny wirtualnej | Maszyna wirtualna. Config.Name
Szczegóły maszyny | Identyfikator serwera vCenter | VMwareClient.InstanceUuid
Szczegóły maszyny |  Opis maszyn wirtualnych |  Maszyna wirtualna. Summary.Config.Annotation
Szczegóły maszyny | Nazwa produktu licencji | vm.Client.ServiceContent.About.LicenseProductName
Szczegóły maszyny | Typ systemu operacyjnego | vm.Summary.Config.GuestFullName
Szczegóły maszyny | Wersja systemu operacyjnego | vm.Summary.Config.GuestFullName
Szczegóły maszyny | Typ rozruchu | vm.Config.Firmware
Szczegóły maszyny | Liczba rdzeni | Maszyna wirtualna. Config.Hardware.NumCPU
Szczegóły maszyny | Pamięć w megabajtach | Maszyna wirtualna. Config.Hardware.MemoryMB
Szczegóły maszyny | Liczba dysków | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk).count
Szczegóły maszyny | Lista rozmiar dysku | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
Szczegóły maszyny | Lista kart sieciowych | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
Szczegóły maszyny | Użycie procesora CPU | cpu.usage.average
Szczegóły maszyny | Użycie pamięci | mem.usage.average
Szczegóły dysku (na dysku) | Wartość klucza na dysku | disk.Key
Szczegóły dysku (na dysku) | Liczba jednostek dysku | disk.UnitNumber
Szczegóły dysku (na dysku) | Wartość klucza kontrolera dysku | dysk. ControllerKey.Value
Szczegóły dysku (na dysku) | Gigabajty zainicjowano obsługę administracyjną | virtualDisk.DeviceInfo.Summary
Szczegóły dysku (na dysku) | Nazwa dysku | Ta wartość jest generowana z użyciem dysku. UnitNumber, dysku. Klucz i dysku. ControllerKey.Value
Szczegóły dysku (na dysku) | Liczba operacji odczytu na sekundę | virtualDisk.numberReadAveraged.average
Szczegóły dysku (na dysku) | Liczba operacji zapisu na sekundę | virtualDisk.numberWriteAveraged.average
Szczegóły dysku (na dysku) | MB na sekundę, przepływności odczytu | virtualDisk.read.average
Szczegóły dysku (na dysku) | MB na sekundę przepływności zapisu | virtualDisk.write.average
Szczegóły karty sieciowej (dla poszczególnych kart Sieciowych) | Nazwa karty sieciowej | karty sieciowej. Klucz
Szczegóły karty sieciowej (dla poszczególnych kart Sieciowych) | Adres MAC | ((VirtualEthernetCard)nic).MacAddress
Szczegóły karty sieciowej (dla poszczególnych kart Sieciowych) | Adresy IPv4 | vm.Guest.Net
Szczegóły karty sieciowej (dla poszczególnych kart Sieciowych) | Adresy IPv6 | vm.Guest.Net
Szczegóły karty sieciowej (dla poszczególnych kart Sieciowych) | MB na sekundę, przepływności odczytu | net.received.average
Szczegóły karty sieciowej (dla poszczególnych kart Sieciowych) | MB na sekundę przepływności zapisu | net.transmitted.average
Szczegóły ścieżki magazynu | Name (Nazwa) | container.GetType().Name
Szczegóły ścieżki magazynu | Typ obiektu podrzędnego | container.ChildType
Szczegóły ścieżki magazynu | Szczegóły odwołania | kontener. MoRef
Szczegóły ścieżki magazynu | Ścieżka pełny spis | kontener. Nazwa z pełną ścieżkę
Szczegóły ścieżki magazynu | Szczegóły nadrzędnego | Container.Parent
Szczegóły ścieżki magazynu | Szczegóły folderu dla każdej maszyny Wirtualnej | ((Folder)container).ChildEntity.Type
Szczegóły ścieżki magazynu | Szczegóły centrum danych dla każdego folderu maszyny Wirtualnej | ((Datacenter)container).VmFolder
Szczegóły ścieżki magazynu | Szczegóły centrum danych dla każdego folderu hosta | ((Datacenter)container).HostFolder
Szczegóły ścieżki magazynu | Szczegóły klastra dla każdego hosta | ((ClusterComputeResource)container).Host)
Szczegóły ścieżki magazynu | Szczegóły hosta dla każdej maszyny Wirtualnej | ((HostSystem) kontenera). Maszyna wirtualna


## <a name="securing-the-collector-appliance"></a>Zabezpieczanie urządzenia modułu zbierającego

Zalecamy następujące kroki, aby zabezpieczyć urządzenie modułu zbierającego:

- Nie można udostępniać lub ze strony nieautoryzowanych zostanie zgubiony przez użytkownika hasła administratora.
- Zamknij urządzenie nieużywane.
- Umieść urządzenie w zabezpieczonej sieci.
- Po zakończeniu migracji należy usunąć wystąpienia urządzenia.
- Ponadto po migracji, również usunąć pliki kopii zapasowej dysków (Vmdk), zgodnie z dysków może być vCenter poświadczenia buforowane na nich.

## <a name="os-license-in-the-collector-vm"></a>Licencja systemu operacyjnego w maszynie Wirtualnej modułu zbierającego.

Moduł zbierający jest powiązana z licencji ewaluacyjnej systemu Windows Server 2012 R2, która jest ważna przez 180 dni. Okres próbny wygaśnie dla swojej maszyny Wirtualnej modułu zbierającego, zalecane jest aby pobrać nowy OVA i utworzyć nowe urządzenie.

## <a name="updating-the-os-of-the-collector-vm"></a>Aktualizowanie systemu operacyjnego maszyny Wirtualnej modułu zbierającego.

Mimo że urządzenie modułu zbierającego ma licencję ewaluacji na okres 180 dni, należy stale aktualizować system operacyjny na urządzeniu, aby uniknąć automatycznego wyłączania urządzenia.

- Jeśli moduł zbierający nie będzie aktualizowany przez 60 dni, rozpocznie automatyczne wyłączanie maszyny.
- Jeśli jest uruchomione odnajdywanie, maszyna nie zostanie wyłączona, nawet jeśli minie 60 dni. Maszyna zostanie wyłączona po ukończeniu odnajdywania.
- Jeśli moduł zbierający był używany przez więcej niż 60 dni, zalecamy regularne aktualizowanie maszyny przez uruchomienie usługi Windows Update.

## <a name="upgrading-the-collector-appliance-version"></a>Uaktualnianie wersji urządzenia modułu zbierającego

Moduł zbierający można uaktualnić do najnowszej wersji, bez pobierania OVA ponownie.

1. Pobierz [najnowszej liście pakietu uaktualnienia](concepts-collector-upgrade.md)
2. Aby upewnij się, że poprawka pobrany bezpieczne, Otwórz okno wiersza polecenia administratora i uruchom następujące polecenie, aby wygenerować wartość skrótu dla pliku ZIP. Wygenerowany skrót powinien odpowiadać za pomocą skrótu wymienionych dla określonej wersji:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (przykład użycia C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Skopiuj plik zip do usługi Azure Migrate collector maszyny wirtualnej (urządzenia modułu zbierającego).
4. Kliknij prawym przyciskiem myszy w pliku zip, a następnie wybierz Wyodrębnij wszystkie.
5. Kliknij prawym przyciskiem myszy na Setup.ps1 i wybierz polecenie Uruchom przy użyciu programu PowerShell, a następnie postępuj zgodnie z instrukcjami na ekranie, aby zainstalować aktualizację.

## <a name="discovery-process"></a>Proces odnajdywania

Po skonfigurowaniu urządzenia można uruchomić odnajdywanie. Poniżej przedstawiono, jak to działa:

- Możesz uruchomić odnajdywanie w zakresie. Wszystkie maszyny wirtualne w ścieżce magazynu określony vCenter zostaną odnalezione.
    - Możesz ustawić jeden zakres w danym momencie.
    - Zakres może obejmować 1500 maszyn wirtualnych lub mniej.
    - Zakres może być centrum danych, folder lub hoście ESXi.
- Po połączeniu się z serwerem vCenter, łączenie, określając dla kolekcji projektu migracji.
- Maszyny wirtualne są wykrywane, a ich metadanych i wydajności dane są wysyłane do platformy Azure. Te akcje są częścią zadania odzyskiwania.
    - Określony identyfikator modułu zbierającego, który jest trwały dla danej maszyny na potrzeby odnajdowania znajduje się urządzenie modułu zbierającego.
    - Uruchomione zadanie odzyskiwania jest podany identyfikator określonej sesji. Identyfikator zmiany dla każdego zadania odzyskiwania i może służyć do rozwiązywania problemów.

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie oceny dla maszyn wirtualnych VMware w środowisku lokalnym](tutorial-assessment-vmware.md)
