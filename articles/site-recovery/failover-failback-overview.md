---
title: Informacje o pracy awaryjnej i powiększaniu po awarii w usłudze Azure Site Recovery
description: Dowiedz się więcej o pracy awaryjnej i pracy awaryjnej w usłudze Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281810"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Informacje o lokalnym odzyskiwaniu po awarii w wersji failover/failback

Ten artykuł zawiera omówienie pracy awaryjnej i powrotu po awarii podczas odzyskiwania po awarii komputerów lokalnych na platformie Azure za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md)

## <a name="recovery-stages"></a>Etapy odzyskiwania

Failover i powiększajuj po awarii w odzyskiwaniu lokacji ma cztery etapy:

- **Etap 1: Przejmowanie awaryjne z lokalnego:** Po skonfigurowaniu replikacji na platformie Azure dla komputerów lokalnych, gdy witryna lokalna ujmuje się, te maszyny są awaryjne na platformie Azure. Po przemieraniu awaryjnego maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych.
- **Etap 2: Ponowne przetwarzanie maszyn wirtualnych platformy Azure:** na platformie Azure ponownie należy ponownie uruchomić maszyny wirtualne platformy Azure, aby rozpocząć replikację z powrotem do lokacji lokalnej. Lokalna maszyna wirtualna (jeśli jest dostępna) jest wyłączona podczas ponownego przekazywania danych, aby zapewnić spójność danych.
- **Etap 3: Tryb failover z platformy Azure:** Gdy witryna lokalna jest uruchomiona normalnie ponownie, uruchom inną funkcję failover, tym razem, aby przywrócić maszyny wirtualne platformy Azure po awarii do lokacji lokalnej. Można przywrócić po awarii do oryginalnej lokalizacji, z której nie powiodło się, lub do lokalizacji alternatywnej.
- **Etap 4: Ponowne przetwarzanie maszyn lokalnych:** po niepowodzeniu w wynikach ponownie włącz replikację komputerów lokalnych na platformie Azure.

## <a name="failover"></a>Tryb failover

Praca awaryjna jest wykonywane w ramach strategii ciągłości biznesowej i odzyskiwania po awarii (BCDR).

- Jako pierwszy krok w strategii BCDR, można replikować komputerów lokalnych na platformie Azure na bieżąco. Użytkownicy uzyskują dostęp do obciążeń i aplikacji uruchomionych na lokalnych komputerach źródłowych.
- Jeśli zajdzie taka potrzeba, na przykład w przypadku awarii w środowisku lokalnym, nie powodowanie replikacji maszyn na platformie Azure. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych.
- Aby uzyskać ciągłość działania, użytkownicy mogą kontynuować uzyskiwanie dostępu do aplikacji na maszynach wirtualnych platformy Azure.

Przewijenie awaryjne jest działaniem dwufazowym:

- **Przewija w błąd:** przewija się w błąd, który tworzy i wywołuje maszynę wirtualną platformy Azure przy użyciu wybranego punktu odzyskiwania.
- **Zatwierdzenie:** Po przewijeniu w prawo pracy awaryjnej można zweryfikować maszynę wirtualną na platformie Azure:
    - Następnie można zatwierdzić pracy awaryjnej do wybranego punktu odzyskiwania lub wybrać inny punkt dla zatwierdzenia.
    - Po pośle podpuszczenie do pracy awaryjnej nie można zmienić punktu odzyskiwania.


## <a name="connect-to-azure-after-failover"></a>Łączenie się z platformą Azure po przełączeniu w ten sposób

Aby połączyć się z maszynami wirtualnymi platformy Azure utworzonymi po przełączeniu w stan failover przy użyciu protokołu RDP/SSH, istnieje wiele wymagań.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure (Windows(** | Na komputerze lokalnym przed przejściem awaryjnym | **Dostęp przez Internet**: Włącz PROW. Upewnij się, że reguły TCP i UDP są dodawane dla **publicznych**i że protokół RDP jest dozwolony dla wszystkich profili w > **aplikacjach dozwolonych zapory** **systemu Windows.**<br/><br/> **Dostęp przez sieć VPN typu lokacja lokacja:** Włącz prowęd na komputerze. Sprawdź, czy usługa RDP jest dozwolona w**aplikacjach i funkcjach dozwolonych przez** **Zaporę** -> systemu Windows dla sieci **domenowych i prywatnych.**<br/><br/>  Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych aktualizacji systemu Windows oczekujących na maszynie Wirtualnej podczas wyzwalania pracy awaryjnej. Usługa Windows Update może zostać uruchomiony po przełączeniu w pracę awaryjną i nie będzie można zalogować się na maszynę wirtualną, dopóki nie zostaną wykonane aktualizacje.
**Maszyna wirtualna platformy Azure z systemem Windows** | Na maszynie Wirtualnej platformy Azure po przemijaniu awaryjnym |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii (i podsieci platformy Azure, do której jest podłączona) muszą zezwalać na połączenia przychodzące z portem RDP.<br/><br/> Sprawdź **diagnostykę rozruchu,** aby zweryfikować zrzut ekranu maszyny Wirtualnej. Jeśli nie możesz nawiązać połączenia, sprawdź, czy maszyna wirtualna jest uruchomiona, i zapoznaj [się ze wskazówkami dotyczącymi rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Maszyna wirtualna platformy Azure z systemem Linux** | Na komputerze lokalnym przed przejściem awaryjnym | Upewnij się, że usługa Secure Shell na maszynie Wirtualnej jest ustawiona na automatyczne uruchamianie podczas rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
**Maszyna wirtualna platformy Azure z systemem Linux** | Na maszynie Wirtualnej platformy Azure po przemijaniu awaryjnym | Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii (i podsieci platformy Azure, do której jest podłączona) muszą zezwalać na połączenia przychodzące z portem SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu,** aby uzyskać zrzut ekranu maszyny Wirtualnej.<br/><br/>

## <a name="types-of-failover"></a>Rodzaje trybu failover

Odzyskiwanie witryny udostępnia różne opcje pracy awaryjnej.

**Tryb failover** | **Szczegóły** | **Odzyskiwanie** | **Przepływ pracy**
--- | --- | --- | ---
**Testowanie pracy w trybie failover** | Służy do uruchamiania wiertła, które sprawdza poprawność strategii BCDR, bez utraty danych lub przestojów.| Tworzy kopię maszyny Wirtualnej na platformie Azure, bez wpływu na trwającą replikację lub na środowisko produkcyjne. | 1. Uruchom test pracy awaryjnej na jednej maszynie wirtualnej lub na wielu maszynach wirtualnych w planie odzyskiwania.<br/><br/> 2. Wybierz punkt odzyskiwania do użycia w przypadku pracy awaryjnej testu.<br/><br/> 3. Wybierz sieć platformy Azure, w której będzie znajdować się maszyna wirtualna platformy Azure, gdy zostanie utworzona po przełączeniu w błąd. Sieć jest używana tylko do pracy awaryjnej testu.<br/><br/> 4. Sprawdź, czy wiertło działało zgodnie z oczekiwaniami. Usługa Site Recovery automatycznie czyści maszyny wirtualne utworzone na platformie Azure podczas drążenia.
**Planowane pracy awaryjnej-Hyper-V**  | Zwykle używane do planowanych przestojów.<br/><br/> Źródłowe maszyny wirtualne są zamykane. Najnowsze dane są synchronizowane przed rozpoczęciem pracy awaryjnej. | Zero utraty danych dla planowanego przepływu pracy. | 1. Zaplanuj okno konserwacji przestojów i powiadom użytkowników.<br/><br/> 2. Przetraktuj aplikacje skierowane do użytkownika w tryb offline.<br/><br/> 3. Zainicjuj planowaną przemijkę awaryjną z najnowszym punktem odzyskiwania. Praca awaryjna nie jest uruchamiana, jeśli komputer nie jest zamknięty lub jeśli wystąpią błędy.<br/><br/> 4. Po przewijaniu awaryjnym sprawdź, czy replika maszyny Wirtualnej platformy Azure jest aktywna na platformie Azure.<br/><br/> 5. Zatwierdzić pracy awaryjnej, aby zakończyć. Akcja zatwierdzania usuwa wszystkie punkty odzyskiwania.
**Funkcja failover-Hyper-V** | Zwykle uruchamia się, jeśli występuje nieplanowana awaria lub lokacja główna nie jest dostępna.<br/><br/> Opcjonalnie zamknij maszynę wirtualną i zsynchronizuj ostateczne zmiany przed rozpoczęciem pracy awaryjnej.  | Minimalna utrata danych w aplikacjach. | 1. Zainicjuj swój plan BCDR. <br/><br/> 2. Inicjuj przemijasz awaryjnie. Określ, czy usługa Site Recovery powinna zamknąć maszynę wirtualną i zsynchronizować/zreplikować najnowsze zmiany przed wyzwoleniem pracy awaryjnej.<br/><br/> 3. Możesz przejść awaryjnie do wielu opcji punktu odzyskiwania, podsumowane w poniższej tabeli.<br/><br/> Jeśli opcja nie zostanie włączona, aby zamknąć maszynę wirtualną lub jeśli odzysk lokacji nie może jej zamknąć, zostanie użyty ostatni punkt odzyskiwania.<br/>Przewijania awaryjnego działa, nawet jeśli nie można wyłączyć urządzenia.<br/><br/> 4. Po przejściu w tryb failover sprawdź, czy replika maszyny Wirtualnej platformy Azure jest aktywna na platformie Azure.<br/> W razie potrzeby można wybrać inny punkt odzyskiwania z okna przechowywania 24 godzin.<br/><br/> 5. Zatwierdzić pracy awaryjnej, aby zakończyć. Akcja zatwierdzania usuwa wszystkie dostępne punkty odzyskiwania.
**Failover-VMware** | Zwykle uruchamia się, jeśli występuje nieplanowana awaria lub lokacja główna nie jest dostępna.<br/><br/> Opcjonalnie należy określić, że usługa Site Recovery należy spróbować wyzwolić zamknięcie maszyny Wirtualnej i zsynchronizować i replikować ostateczne zmiany przed rozpoczęciem pracy awaryjnej.  | Minimalna utrata danych w aplikacjach. | 1. Zainicjuj swój plan BCDR. <br/><br/> 2. Zainicjuj od pracy awaryjnej z odzyskiwania witryny. Określ, czy usługa Site Recovery powinna próbować wyzwolić zamknięcie maszyny Wirtualnej i zsynchronizować przed uruchomieniem trybu failover.<br/> Przewijania awaryjnego działa, nawet jeśli nie można wyłączyć maszyn.<br/><br/> 3. Po przewijaniu awaryjnym sprawdź, czy replika maszyny Wirtualnej platformy Azure jest aktywna na platformie Azure. <br/>W razie potrzeby można wybrać inny punkt odzyskiwania z okna przechowywania 72 godzin.<br/><br/> 5. Zatwierdzić pracy awaryjnej, aby zakończyć. Akcja zatwierdzania usuwa wszystkie punkty odzyskiwania.<br/> W przypadku maszyn wirtualnych z systemem Windows funkcja Odzysk witryny wyłącza narzędzia VMware podczas pracy awaryjnej.

## <a name="failover-processing"></a>Przetwarzanie trybu failover

W niektórych scenariuszach praca awaryjna wymaga dodatkowego przetwarzania, które trwa około 8 do 10 minut. Można zauważyć dłuższe czasy pracy awaryjnej testu dla:

* Maszyny wirtualne VMware z uruchomieniem wersji usługi mobilności starszej niż 9.8.
* Serwery fizyczne.
* Maszyny wirtualne VMware z systemem Linux.
* Maszyny wirtualne funkcji Hyper V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opcje punktu odzyskiwania

Podczas pracy awaryjnej można wybrać kilka opcji punktu odzyskiwania.

**Opcja** | **Szczegóły**
--- | ---
**Najnowsze (najniższy RPO)** | Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO). Najpierw przetwarza wszystkie dane, które zostały wysłane do usługi site recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej, przed wdrążanie jej w stan niepowodzenia. Ten punkt odzyskiwania ma wszystkie dane replikowane do usługi Site Recovery po wyzwoleniu pracy awaryjnej.
**Najnowsze przetworzone**  | Ta opcja jest awaryjna za pomocą maszyn wirtualnych do najnowszego punktu odzyskiwania przetwarzanego przez odzyskiwanie witryny. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny Wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
**Najnowsze spójne z aplikacjami** |  Ta opcja jest awaryjna za pomocą maszyn wirtualnych do najnowszego punktu odzyskiwania spójnego z aplikacją przetwarzanego przez funkcję Site Recovery, jeśli są włączone punkty odzyskiwania spójne z aplikacją. Sprawdź najnowszy punkt odzyskiwania w ustawieniach maszyny Wirtualnej.
**Najnowsze przetwarzanie wielu maszyn wirtualnych** | Ta opcja jest dostępna dla planów odzyskiwania z co najmniej jedną maszyną wirtualną, która ma włączoną spójność wielu maszyn wirtualnych. Maszyny wirtualne z włączoną włączoną funkcją fail over do najnowszego wspólnego wspólnego punktu odzyskiwania spójnego multi-VM. Wszystkie inne maszyny wirtualne w planie w pracy awaryjnej do ostatniego punktu odzyskiwania przetworzone.
**Najnowsze aplikacje z wieloma maszynami wirtualnymi** |  Ta opcja jest dostępna dla planów odzyskiwania z co najmniej jedną maszyną wirtualną, która ma włączoną spójność wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji w trybie fail over do najnowszego wspólnego wspólnego punktu odzyskiwania spójne aplikacji wielu maszyn wirtualnych. Inne maszyny wirtualne w pracy awaryjnej do ich ostatni punkt odzyskiwania spójne aplikacji.
**Niestandardowy** | Użyj tej opcji, aby w czasie przewinąć w stan faila dla określonej maszyny Wirtualnej do określonego punktu odzyskiwania. Ta opcja nie jest dostępna dla planów odzyskiwania.

> [!NOTE]
> Punktów odzyskiwania nie można migrować do innego magazynu usług odzyskiwania.

## <a name="reprotectionfailback"></a>Ponowne wycofywanie/powrót po awarii

Po przewijaniu awaryjnym na platformie Azure replikowane maszyny wirtualne platformy Azure są w stanie niechronionym.

- Jako pierwszy krok do niepowodzenia z powrotem do lokacji lokalnej, należy uruchomić maszyny wirtualne platformy Azure replikacji do lokalnego. Proces ponownego przetwarzania zależy od typu maszyn, które zostały przejęte awaryjnie.
- Po maszyn są replikowane z platformy Azure do lokalnego, można uruchomić pracy awaryjnej z platformy Azure do witryny lokalnej.
- Po ponownym uruchomieniu komputera lokalnego można włączyć replikację, aby replikować na platformie Azure w celu odzyskiwania po awarii.

Powrót po awarii działa w następujący sposób:

- Aby przywrócić po awarii, maszyna wirtualna potrzebuje co najmniej jednego punktu odzyskiwania, aby wrócić awaryjnie. W planie odzyskiwania wszystkie maszyny wirtualne w planie potrzebują co najmniej jednego punktu odzyskiwania.
- Zaleca się użycie **ostatniego** punktu odzyskiwania do powrotu po awarii (jest to punkt spójny z awarią).
    - Istnieje opcja punktu odzyskiwania spójnego z aplikacją. W takim przypadku pojedyncza maszyna wirtualna odzyskuje do najnowszego dostępnego punktu odzyskiwania spójnego z aplikacją. W przypadku planu odzyskiwania z grupą replikacji każda grupa replikacji odzyskuje do wspólnego dostępnego punktu odzyskiwania.
    - Punkty odzyskiwania spójne z aplikacjami mogą być w tyle w czasie i może wystąpić utrata danych.
- Podczas pracy awaryjnej z platformy Azure do lokacji lokalnej usługa Site Recovery zamyka maszyny wirtualne platformy Azure. Po zatwierdzeniu pracy awaryjnej usługa Site Recovery usuwa nieudane maszyny wirtualne platformy Azure na platformie Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fizyczne ponowne odtwarzanie/powrót po awarii

Aby ponownie wykonać i zakończyć po awarii maszyny VMware i serwery fizyczne z platformy Azure do lokalnego, potrzebujesz infrastruktury powrotu po awarii i istnieje wiele wymagań.

- **Tymczasowy serwer procesów na platformie Azure:** Aby odzyskać po awarii z platformy Azure, skonfigurować maszynę wirtualną platformy Azure, aby działać jako serwer procesów do obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
- **Połączenie sieci VPN:** Aby przywrócić po awarii, potrzebujesz połączenia sieci VPN (lub usługi ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
- **Oddzielny główny serwer docelowy:** Domyślnie główny serwer docelowy zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware obsługuje powrót zwrotny po awarii. Jeśli konieczne jest przywrócenie dużych ilości ruchu, należy skonfigurować w tym celu oddzielny lokalny główny serwer docelowy.
- **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Ta zasada jest tworzona automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
    - Ta zasada jest automatycznie skojarzona z serwerem konfiguracji.
    - Nie można edytować tych zasad.
    - Wartości zasad: próg RPO - 15 minut; Zatrzymanie punktu odzyskiwania - 24 godziny; Częstotliwość migawek spójna z aplikacją — 60 minut.

Dowiedz się więcej o vmware/fizycznej reprotection i powrotu po awarii:
- [Przejrzyj](vmware-azure-reprotect.md#before-you-begin) dodatkowe wymagania dotyczące ponownego wycofywania i powrotu po awarii.
- [Wdrażanie](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) serwera przetwarzania na platformie Azure.
- [Wdrażanie](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) oddzielnego głównego serwera docelowego.

Po ponownej ceł maszyny wirtualnej platformy Azure do lokalnego, można określić, że chcesz wrócić po awarii do oryginalnej lokalizacji lub do lokalizacji alternatywnej.

- **Odzyskiwanie oryginalnej lokalizacji:** to kończy się niepowodzeniem z platformy Azure do tego samego źródła komputera lokalnego, jeśli istnieje. W tym scenariuszu tylko zmiany są replikowane z powrotem do lokalnego.
- **Odzyskiwanie lokalizacji alternatywnej:** Jeśli komputer lokalny nie istnieje, można przywrócić po awarii z platformy Azure do lokalizacji alternatywnej. Po ponownej obrysu maszyny Wirtualnej platformy Azure do lokalnego, komputera lokalnego jest tworzony. Pełna replikacja danych odbywa się z platformy Azure do lokalnego. - - [Przejrzyj](concepts-types-of-failback.md) wymagania i ograniczenia dotyczące powrotu po awarii lokalizacji.



## <a name="hyper-v-reprotectionfailback"></a>Reprotection/powrót po awarii funkcji Hyper-V

Aby ponownie przesłać i przywrócić po awarii maszyny wirtualne z platformy Azure do lokalnej:

- Można tylko po awarii z powrotem hiper-V maszyny wirtualne replikowania przy użyciu konta magazynu. Powrót zwrotny maszyn wirtualnych funkcji Hyper-V, które są replikowane przy użyciu dysków zarządzanych, nie jest obsługiwany.
- Lokalne hosty funkcji Hyper-V (lub program Windows VMM, jeśli są używane) powinny być połączone z platformą Azure.
- Uruchomisz planowany powrót po awarii z platformy Azure do lokalnego.
- Nie należy łączeć żadnych określonych składników dla powrotu po awarii maszyny Wirtualnej funkcji Hyper-V.
- Podczas planowanego trybu failover można wybrać opcje synchronizacji danych przed powrotem po awarii:
    - **Synchronizacja danych przed trybem failover:** Ta opcja minimalizuje przestoje maszyn wirtualnych, ponieważ synchronizuje maszyny bez zamykania ich.
        - Faza 1: Wykonuje migawkę maszyny Wirtualnej platformy Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Komputer kontynuuje uruchamianie na platformie Azure.
        - Faza 2: Zamyka maszynę wirtualną platformy Azure, dzięki czemu nie występują żadne nowe zmiany. Ostatni zestaw zmian delta jest przenoszony do serwera lokalnego, a lokalna maszyna wirtualna jest uruchamiana.
    - **Synchronizuj dane tylko podczas pracy awaryjnej:** Ta opcja jest szybsza, ponieważ oczekujemy, że większość dysku została zmieniona, a tym samym nie należy wykonywać obliczeń sumy kontrolnej. Wykonuje pobieranie dysku. Zaleca się użycie tej opcji, jeśli maszyna wirtualna jest uruchomiona na platformie Azure przez jakiś czas (miesiąc lub dłużej) lub jeśli lokalna maszyna wirtualna została usunięta.

[Dowiedz się więcej](hyper-v-azure-failback.md) o ponownej rekonsekcji funkcji Hyper-V i po awarii.

Po ponownej ceł maszyny wirtualnej platformy Azure do lokalnego, można określić, że chcesz wrócić po awarii do oryginalnej lokalizacji lub do lokalizacji alternatywnej.

- **Odzyskiwanie oryginalnej lokalizacji:** to kończy się niepowodzeniem z platformy Azure do tego samego źródła komputera lokalnego, jeśli istnieje. W tym scenariuszu należy wybrać jedną z opcji synchronizacji opisanych w poprzedniej procedurze.
- **Odzyskiwanie lokalizacji alternatywnej:** Jeśli komputer lokalny nie istnieje, można przywrócić po awarii z platformy Azure do lokalizacji alternatywnej. Po ponownej obrysu maszyny Wirtualnej platformy Azure do lokalnego, komputera lokalnego jest tworzony. W tym za pomocą tej opcji zaleca się wybranie opcji synchronizacji danych przed
- [Przejrzyj](hyper-v-azure-failback.md) wymagania i ograniczenia dotyczące powrotu po awarii lokalizacji.


Po niepowodzeniu powrotu do lokacji lokalnej można włączyć **reverse replicate,** aby rozpocząć replikowanie maszyny Wirtualnej na platformę Azure, ukończenie cyklu.




## <a name="next-steps"></a>Następne kroki
- Maszyny [wirtualne vmware](vmware-azure-tutorial-failover-failback.md) w wersji fail over
- W pracy [awaryjnej określonych maszyn wirtualnych z programem Hyper-V](hyper-v-azure-failover-failback-tutorial.md).
- [Tworzenie](site-recovery-create-recovery-plans.md) planu odzyskiwania.
- Maszyny [wirtualne w](site-recovery-failover.md)wersji fail over w planie odzyskiwania .
- [Przygotuj się na](vmware-azure-failback.md) Reprotection VMware i powrót po awarii.
- Po awarii maszyny [wirtualne z programem Hyper-V](hyper-v-azure-failback.md).

