---
title: Najlepsze rozwiązania dotyczące tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Zawiera opis najlepszych rozwiązań dotyczących wdrażania i zarządzania nimi tablicy wirtualne StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: alkohli
ms.openlocfilehash: 46fd818d8ca15515c91bb6e65e99b0a3bc1f1fa4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972844"
---
# <a name="storsimple-virtual-array-best-practices"></a>Najlepsze rozwiązania w zakresie tablicy wirtualnego StorSimple
## <a name="overview"></a>Przegląd
Microsoft Azure StorSimple wirtualnego tablicy to rozwiązanie zintegrowanego magazynu zarządzanego zadań magazynu między lokalnymi urządzenia wirtualnego działający w funkcji hypervisor i magazynu w chmurze Microsoft Azure. Tablica wirtualne StorSimple jest to alternatywa wydajne i ekonomiczne do tablicy fizycznej serii 8000. Wirtualny tablicy można uruchomić w istniejącej infrastrukturze funkcji hypervisor, obsługuje zarówno iSCSI i protokoły SMB i dobrze nadaje się do scenariuszach dotyczących biura zdalnego/oddział. Aby uzyskać więcej informacji na temat rozwiązań StorSimple, przejdź do [Przegląd usługi Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

W tym artykule omówiono najlepsze rozwiązania zaimplementowana podczas początkowej konfiguracji, wdrażania i zarządzania tablicy wirtualne StorSimple. Następujące najlepsze rozwiązania zamieszczono wytyczne zweryfikowanych Konfiguracja i zarządzanie macierzy wirtualnego. W tym artykule jest przeznaczona do administratorów IT, którzy wdrażania i zarządzania nimi wirtualnego tablic w swoich centrach danych.

Zalecamy okresowe analizy najlepszych rozwiązań, aby upewnić się, że urządzenie jest nadal zgodności podczas wprowadzania zmian do przepływu konfiguracji lub operacji. Należy wystąpią problemy podczas implementowania następujące najlepsze rozwiązania w sieci wirtualnej macierzy [skontaktuj się z Microsoft Support](storsimple-virtual-array-log-support-ticket.md) Aby uzyskać pomoc.

## <a name="configuration-best-practices"></a>Najlepsze rozwiązania w zakresie konfiguracji
Następujące najlepsze rozwiązania obejmują wskazówki, które powinny być kontynuowane podczas początkowej konfiguracji i wdrażania wirtualnego tablic. Następujące najlepsze rozwiązania obejmują problemy dotyczące inicjowania obsługi maszyny wirtualnej, ustawienia zasad grupy, zmiany rozmiaru, trwa konfigurowanie sieci konfigurowania kont magazynu i tworzenie udziałów i woluminów na potrzeby wirtualnego tablicy. 

### <a name="provisioning"></a>Inicjowanie obsługi
Tablica wirtualnego StorSimple jest inicjowana na funkcji hypervisor (Hyper-V lub programu VMware) maszyny wirtualnej (VM) serwera hosta. Podczas inicjowania obsługi administracyjnej maszyny wirtualnej, upewnij się, że host jest w stanie dedykować wystarczające zasoby. Aby uzyskać więcej informacji, przejdź do [minimalnych wymagań dotyczących zasobów](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) do udostępnienia tablicy.

Podczas inicjowania obsługi administracyjnej wirtualnego tablicy, należy zaimplementować następujące najlepsze rozwiązania:

|  | Funkcja Hyper-V | VMware |
| --- | --- | --- |
| **Typ maszyny wirtualnej** |**Generacja 2** maszyny Wirtualnej do użytku z systemem Windows Server 2012 lub nowszym i *vhdx* obrazu. <br></br> **Generacja 1** maszyny Wirtualnej do użytku z systemem Windows Server 2008 lub nowszym i *VHD* obrazu. |Maszyny wirtualnej wersji 8 używając *.vmdk* obrazu. |
| **Typ pamięci** |Należy skonfigurować jako **pamięci statycznej**. <br></br> Nie używaj **pamięci dynamicznej** opcji. | |
| **Typ dysku danych** |Ustanowić jako **dynamicznie powiększających się**.<br></br> **Stały rozmiar** zajmuje dużo czasu. <br></br> Nie używaj **różnicowych** opcji. |Użyj **elastycznej udostępniania** opcji. |
| **Modyfikowanie dysku danych** |Rozszerzenie lub zmniejszanie jest niedozwolone. Próba powoduje utratę danych lokalnych na urządzeniu. |Rozszerzenie lub zmniejszanie jest niedozwolone. Próba powoduje utratę danych lokalnych na urządzeniu. |

### <a name="sizing"></a>Zmiana rozmiaru
Podczas zmiany rozmiaru wirtualnych tablica StorSimple, należy wziąć pod uwagę następujące czynniki:

* Lokalne rezerwacji woluminy lub udziały. 12% miejsca jest zarezerwowana w warstwie lokalnego dla każdego woluminu warstwowego elastycznie lub udziału. Około 10% miejsca również jest zarezerwowana dla woluminu przypiętego lokalnie systemu plików.
* Narzut migawki. Około 15% miejsca na warstwie lokalnej jest zarezerwowana dla migawki.
* Potrzebę operacji przywracania. Jeśli podczas przywracania jako nową operację, zmiany rozmiaru należy uwzględnić miejsce wymagane do przywracania. Przywracania odbywa się na udziale lub woluminie ten sam rozmiar.
* Niektóre buforu powinna zostać przydzielona dla dowolnego nieoczekiwany wzrostu.

Oparte na czynnikach poprzedniego, wymagania dotyczące zmiany rozmiaru, może być reprezentowany przez następujące równanie:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Poniższe przykłady przedstawiają, jak dostosować rozmiar wirtualnego tablicy, w zależności od wymagań.

#### <a name="example-1"></a>Przykład 1:
W sieci wirtualnej macierzy chcesz mieć możliwość

* Zapewnij 2 TB warstwowych woluminu lub udziału.
* Zapewnij 1 TB warstwowych woluminu lub udziału.
* Zapewnij 300 GB miejsca woluminu przypiętego lokalnie lub udziału.

Dla poprzedniego woluminy lub udziały Poinformuj nas obliczyć wymagania dotyczące miejsca na warstwie lokalnej.

Najpierw każdego warstwowych woluminu i udostępniania, rezerwacji lokalnej będzie równą % 12 rozmiaru woluminu i udostępniania. Dla woluminu przypiętego lokalnie i udostępniania lokalne rezerwacji wynosi 10% rozmiaru woluminu przypiętego lokalnie i udostępniania (oprócz elastycznie rozmiaru). W tym przykładzie należy

* Zastrzeżenie lokalnego 240 GB (dla 2 TB warstwowa woluminu i udostępniania)
* Zastrzeżenie lokalnego 120 GB (dla 1 TB warstwowa woluminu i udostępniania)
* 330 GB dla woluminu przypiętego lokalnie lub w udziale (Dodawanie 10% rezerwacji lokalnego do rozmiaru 300 GB udostępnione)

Całkowita ilość miejsca na warstwie lokalnej do tej pory wymagane jest: 240 GB + 120 GB + 330 GB = 690 GB.

Po drugie potrzebujemy co najmniej taką ilość miejsca na warstwie lokalnej jako największy rezerwacji pojedynczego. Kwota dodatkowa ta jest używana w razie potrzeby przywrócenie z migawki w chmurze. W tym przykładzie największy rezerwacji lokalnego jest 330 GB (w tym zastrzeżenie dotyczące systemu plików), dlatego należy dodać do 690 GB: 690 GB + 330 GB = 1020 GB.
Jeśli wykonane kolejne dodatkowe operacji przywracania, możemy zawsze zwolnić miejsce z poprzedniej operacji przywracania.

Trzecie potrzebujemy 15% całkowitej lokalne miejsce do tej pory do przechowywania migawek lokalnych, tak aby była dostępna tylko 85%. W tym przykładzie, który będzie wokół 1020 GB = 0.85&ast;elastycznie danych na dysku TB. Tak, będzie dysk z danymi elastycznie (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (zaokrąglania do najbliższej kwartyl)

Factoring w nieoczekiwany wzrostu i przywraca nowe, należy udostępnić dysku lokalnym wokół 1,25-1,5 TB.

> [!NOTE]
> Zalecane jest również, że dysk lokalny jest alokowany elastycznie. To zalecenie wynika przywracania miejsca jest potrzebne tylko wtedy, gdy chcesz przywrócić dane, które jest starszy niż pięć dni. Odzyskiwanie na poziomie elementu umożliwia przywrócenie danych w ciągu ostatnich pięciu dni bez konieczności dodatkowe miejsce do przywrócenia.


#### <a name="example-2"></a>Przykład 2:
W sieci wirtualnej macierzy chcesz mieć możliwość

* Zapewnij 2 TB wolumin warstwowy
* Obsługiwanie woluminu 300 GB przypięty lokalnie

W oparciu o 12% rezerwacji lokalne miejsce w przypadku woluminów warstwowych/udziałów i 10% dla woluminów przypiętych lokalnie/udziałów, potrzebujemy

* Zastrzeżenie lokalnego 240 GB (dla 2 TB warstwowa woluminu i udostępniania)
* 330 GB dla woluminu przypiętego lokalnie lub w udziale (Dodawanie 10% rezerwacji lokalnego do obszaru 300 GB udostępnione)

Jest wymagane w warstwie lokalnej całkowita ilość miejsca: 240 GB + 330 GB = 570 GB

Minimalna lokalne miejsce potrzebne do odzyskiwania jest 330 GB.

15% całkowitej dysk jest używany do przechowywania migawek, tak aby tylko 0.85 jest dostępna. Dlatego rozmiar dysku jest (900&ast;(1/0.85)) = 1.06 TB ~ 1,25 TB (zaokrąglania do najbliższej kwartyl)

Factoring wszelkie nieoczekiwane wzrostu, można udostępnić dysk lokalny 1,25-1,5 TB.

### <a name="group-policy"></a>Zasady grupy
Zasady grupy to infrastruktura umożliwiająca do zaimplementowania konfiguracji określonych dla użytkowników i komputerów. Ustawienia zasad grupy są zawarte w obiektach zasad grupy (GPO), które są połączone z następujących kontenerów usług domenowych w usłudze Active Directory (AD DS): lokacje, domeny i jednostki organizacyjne (OU). 

Jeśli tablica wirtualnego jest przyłączony do domeny, obiektów zasad grupy można zastosować do niej. Te obiekty zasad grupy można zainstalować aplikacje, takie jak oprogramowanie antywirusowe, które może niekorzystnie wpływać na działanie macierzy wirtualne StorSimple.

Dlatego zaleca się, że możesz:

* Upewnij się, jest tablica wirtualnego w jego własnej jednostce organizacyjnej (OU) usługi Active Directory.
* Upewnij się, że żadne obiekty zasad grupy (GPO) są stosowane do wirtualnego macierzy. Możesz zablokować dziedziczenia, aby upewnić się, że wirtualny tablicy (węzła podrzędnego) nie automatycznie dziedziczy obiektów zasad grupy z obiektu nadrzędnego. Aby uzyskać więcej informacji, przejdź do [zablokować dziedziczenie](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Networking
W konfiguracji sieci wirtualnej tablica odbywa się za pośrednictwem lokalnego interfejsu użytkownika sieci web. Interfejs sieci wirtualnej jest włączona za pomocą funkcji hypervisor, w którym zostanie zainicjowana wirtualnego tablicy. Użyj [ustawienia sieciowe](storsimple-virtual-array-deploy3-fs-setup.md) strony, aby skonfigurować adres IP interfejsu sieci wirtualnej, podsieci i bramy.  Można również skonfigurować podstawowego i pomocniczego serwera DNS, ustawienia czasu i opcjonalne ustawienia serwera proxy dla danego urządzenia. W większości konfiguracji sieci jest jednorazowej konfiguracji. Przegląd [StorSimple wymagań sieciowych](storsimple-ova-system-requirements.md#networking-requirements) przed wdrożeniem wirtualnego tablicy.

W przypadku wdrażania wirtualnego tablica, firma Microsoft zaleca, należy stosować następujące najlepsze rozwiązania:

* Sprawdź, czy sieci, w którym wirtualnego tablicy jest wdrożony zawsze ma zdolność do dedykować 5 przepustowości połączenia z Internetem MB/s (lub więcej).
  
  * Konieczność przepustowości Internetu różni się w zależności od Twojego cechy obciążenia i szybkość zmian danych.
  * Zmiany danych, które mogą być obsługiwane jest wprost proporcjonalny do przepustowości połączenia z Internetem. Na przykład podczas wykonywania kopii zapasowej 5 MB/s przepustowości może obsłużyć zmiana danych około 18 GB 8 godzin. Cztery razy większe wartości przepustowości (20 MB/s) może obsługiwać cztery razy więcej zmian danych (72 GB).
* Upewnij się, że łączność z Internetem jest zawsze dostępna. Sporadyczne lub zawodnych połączeń internetowych z urządzeniami może spowodować utratę dostępu do danych w chmurze i może skutkować nieobsługiwaną konfiguracją.
* Jeśli planujesz wdrożyć urządzenie jako serwer iSCSI:
  
  * Firma Microsoft zaleca, aby wyłączyć **uzyskać adres IP automatycznie** opcji (DHCP).
  * Skonfiguruj statyczne adresy IP. Należy skonfigurować podstawowy i pomocniczy serwer DNS.
  * Jeśli Definiowanie wiele interfejsów sieciowych na wirtualnej tablicy, tylko pierwszy interfejsu sieciowego (domyślnie ten interfejs jest **Ethernet**) może nawiązać połączenie z chmury. Aby kontrolować typ ruchu sieciowego, można utworzyć wiele interfejsów sieci wirtualnej na wirtualny tablica (skonfigurowany jako serwer iSCSI) i połącz te interfejsy do różnych podsieci.
* Aby ograniczyć przepustowość chmury tylko (wykorzystywane przez tablicy wirtualnego), skonfigurować ograniczenie na routerze lub zapory. W przypadku definiowania ograniczania przepustowości w Twojej funkcji hypervisor, zostanie ograniczenie przepustowości wszystkich protokołów, w tym iSCSI i SMB zamiast tylko przepustowości chmury.
* Upewnij się, że synchronizacja czasu jest włączona w funkcji hypervisor. Jeśli za pomocą funkcji Hyper-V, wybierz tablica wirtualnych w Menedżerze funkcji Hyper-V, przejdź do **ustawienia &gt; usług integracji**i upewnij się, że **synchronizacja czasu** jest zaznaczony.

### <a name="storage-accounts"></a>Konta magazynu
Tablica wirtualnego StorSimple może być skojarzony z kontem magazynu jednego. To konto magazynu może być konto magazynu automatycznie generowanych konto w tej samej subskrypcji co w usłudze lub konto magazynu powiązane z innej subskrypcji. Aby uzyskać więcej informacji, zobacz temat jak [Zarządzanie kontami magazynu dla wirtualnego tablica](storsimple-virtual-array-manage-storage-accounts.md).

Używaj poniższych zaleceń dla konta magazynu skojarzone z sieci wirtualnej tablicy.

* Podczas łączenia wielu wirtualnych tablic z kontem magazynu pojedynczego, współczynnik maksymalnej pojemności (64 TB) dla tablicy wirtualnego i maksymalny rozmiar (500 TB) dla konta magazynu. To ogranicza liczbę pełnym rozmiarze tablice wirtualnych, które mogą być powiązane z tym kontem magazynu do około 7.
* Podczas tworzenia nowego konta magazynu
  
  * Firma Microsoft zaleca utworzenie go w regionie najbliżej zdalnego office/oddziału wdrożonym tablica wirtualnego StorSimple do minimalizują opóźnienia.
  * Przy tym pamiętać, że nie można przenieść konto magazynu w różnych regionach. Ponadto nie można przenieść usługi różnych subskrypcji.
  * Użyć konta magazynu, które implementuje nadmiarowość między centrach danych. Magazyn geograficznie nadmiarowy (GRS), strefy magazyn geograficznie nadmiarowy (ZRS) i lokalnie nadmiarowego magazynu (LRS) są obsługiwane do użytku z sieci wirtualnej tablicy. Aby uzyskać więcej informacji na temat różnych typów kont magazynu, przejdź do [replikacji usługi Azure storage](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Woluminów i udziałów
Dla macierzy wirtualnego StorSimple można udostępnić udziałów, gdy jest on skonfigurowany jako serwer plików i woluminy, gdy jest skonfigurowany jako serwer iSCSI. Najlepsze rozwiązania dotyczące tworzenia woluminów i udziałów odnoszą się do rozmiar i typ skonfigurowany.

#### <a name="volumeshare-size"></a>Rozmiar woluminu i udostępniania
W sieci wirtualnej tablicy można udostępnić udziałów, gdy jest on skonfigurowany jako serwer plików i woluminy, gdy jest skonfigurowany jako serwer iSCSI. Najlepsze rozwiązania dotyczące tworzenia woluminów i udziałów odnoszą się do rozmiar i typ skonfigurowany. 

Wziąć pod uwagę następujące najlepsze rozwiązania podczas inicjowania obsługi administracyjnej udziały lub woluminy na urządzeniu wirtualnym.

* Rozmiary plików elastycznie rozmiaru udziału warstwowych może wpłynąć na wydajność warstw. Praca z dużych plików może spowodować wolne warstwy w poziomie. Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż rozmiar udziału % 3.
* Na tablicy wirtualnego można utworzyć maksymalnie 16 woluminów/udziałów. Limity rozmiaru woluminów warstwowych i przypiętych lokalnie/udziałów, zawsze można znaleźć w temacie [ogranicza tablicy wirtualnego StorSimple](storsimple-ova-limits.md).
* Podczas tworzenia woluminu czynnikiem zużycie oczekiwane dane, a także rozwój w przyszłości. Wolumin nie można rozwijać później.
* Gdy wolumin został utworzony, nie można zmniejszyć rozmiaru woluminu na StorSimple.
* Podczas zapisywania do woluminu warstwowego na StorSimple, gdy wolumin danych osiągnie określony próg (względem lokalne miejsce zarezerwowane dla woluminu), we/wy jest ograniczony. Kontynuuje zapis do tego woluminu spowalnia we/wy znacznie. Chociaż możesz zapisywać do woluminu warstwowego ponad jego pojemność elastycznie (Firma Microsoft nie aktywnie zatrzymywać użytkownika zapisywaniu poza pojemność elastycznie), zostanie wyświetlony w celu, który ma oversubscribed powiadomień o alertach. Gdy zostanie wyświetlony alert, należy bezwzględnie wykonać środki zaradcze, takich jak usuwanie danych woluminu (wolumin rozwijanie aktualnie nie jest obsługiwane).
* W przypadki użycia odzyskiwania po awarii liczbę dopuszczalnych udziałów/woluminów 16 i maksymalną liczbę akcji/woluminów, które mogą być przetwarzane równolegle jest również 16, liczba woluminów/udziały nie ma wpływu na RPO i Objective, RTO.

#### <a name="volumeshare-type"></a>Typ woluminu i udostępniania
StorSimple obsługuje dwa typy woluminu i udostępniania, na podstawie użycia: lokalnie przypięty i warstwy. Woluminów przypiętych lokalnie/udziały są alokowany nieelastycznie, podczas gdy woluminów warstwowych/udziały są alokowane elastycznie. Nie można przekonwertować woluminu przypiętego lokalnie i udostępniania do warstwowego lub *odwrotnie* raz utworzony.

Zaleca się wdrożenie następujące najlepsze rozwiązania, podczas konfigurowania woluminów StorSimple/udziałów:

* Określ typ woluminu oparte na obciążeń, które mają zostać wdrożone przed utworzeniem woluminu. Użyj woluminów przypiętych lokalnie dla obciążeń, które wymagają lokalnych gwarancji danych (nawet podczas awarii chmury) i które wymagają opóźnienia niski chmury. Po utworzeniu woluminu w sieci wirtualnej tablicy nie można zmienić typu woluminu z przypięty lokalnie do warstwowego lub *odwrotnie*. Na przykład tworzenie woluminów przypiętych lokalnie, podczas wdrażania obciążeń SQL lub obciążeń hostowania maszyn wirtualnych (VM); Użyj woluminy warstwowe dla obciążeń udziału plików.
* Zaznacz opcję dla rzadziej używanych danych archiwalnych, podczas pracy nad duże rozmiary plików. Większy rozmiar fragmentu deduplikacji 512 KB jest używany, gdy ta opcja jest włączona usprawnienie transferu danych do chmury.

#### <a name="volume-format"></a>Formatowanie woluminu
Po utworzeniu woluminy StorSimple na serwerze sieci iSCSI, należy zainicjować, zainstalować i sformatować woluminy. Ta operacja odbywa się na hoście podłączony do urządzenia StorSimple. Następujące najlepsze rozwiązania są zalecane w przypadku instalowania i formatowanie woluminów StorSimple hosta.

* Przeprowadź szybkie formatowanie na wszystkich woluminów StorSimple.
* Podczas formatowania woluminu StorSimple, użyj rozmiaru jednostki alokacji (AUS) 64 KB (wartość domyślna to 4 KB). 64 KB AUS opiera się na testowania wykonywane we własnym zakresie typowych obciążeń StorSimple oraz innych obciążeń.
* Korzystając z tablicy wirtualnego StorSimple, skonfigurowany jako serwer iSCSI, nie używaj łączony woluminy lub dyski dynamiczne woluminy lub dyski nie są obsługiwane przez StorSimple.

#### <a name="share-access"></a>Dostęp do udziału
Podczas tworzenia udziałów na serwerze plików wirtualnych tablicy, skorzystaj z następujących wskazówek:

* Podczas tworzenia udziału, Przypisz grupę użytkowników jako administrator udziału zamiast pojedynczego użytkownika.
* Po utworzeniu udziału, edytując akcji przy użyciu Eksploratora Windows, można zarządzać uprawnienia systemu plików NTFS.

#### <a name="volume-access"></a>Dostęp do woluminu
Podczas konfigurowania woluminów iSCSI tablica wirtualnego StorSimple, warto kontroli dostępu w razie konieczności. Aby ustalić, które serwery hosta można uzyskać dostęp do woluminów, tworzenie i skojarz rekordy kontroli dostępu (ACRs) z woluminów StorSimple.

Podczas konfigurowania ACRs woluminów StorSimple, należy używać następujących rozwiązań:

* Zawsze należy skojarzyć ACR co najmniej jeden z woluminu.

* Podczas przypisywania ACR więcej niż jeden wolumin, upewnij się, że wolumin nie jest uwidaczniana w taki sposób, w którym jest jednocześnie dostępny przez więcej niż jeden host nieklastrowany. Jeśli przypisano wiele ACRs do woluminu, ostrzeżenie pojawia się należy przejrzeć konfigurację.

### <a name="data-security-and-encryption"></a>Bezpieczeństwo danych i szyfrowania
Tablica wirtualnego StorSimple ma funkcje zabezpieczeń i szyfrowania danych, które zapewnić poufność i integralność danych. Korzystając z tych funkcji, zalecane jest, należy stosować następujące najlepsze rozwiązania: 

* Zdefiniuj klucz szyfrowania magazynu w chmurze do generowania szyfrowania AES 256 przed wysłaniem danych z tablicy, sieci wirtualnych do chmury. Ten klucz nie jest wymagane, jeśli dane są szyfrowane rozpoczynać się od znaku. Klucz może być wygenerowany i były bezpieczne, przy użyciu systemu zarządzania kluczami, takich jak [usługi Azure key vault](../key-vault/key-vault-whatis.md).
* Podczas konfigurowania konta magazynu za pomocą usługi Menedżer StorSimple, upewnij się, że Włącz tryb SSL, można utworzyć bezpiecznego kanału komunikacji sieciowej między urządzeniem StorSimple i chmury.
* Wygeneruj ponownie klucze kont magazynu (uzyskując dostęp do usługi Magazyn Azure) okresowo do konta w celu zmiany, aby uzyskać dostęp do na podstawie zmienione listy administratorów.
* Dane dotyczące wirtualnych tablica jest skompresowany i deduplikowany przed wysłaniem do platformy Azure. Nie zaleca się korzystanie z usługi roli deduplikacji danych na hoście z systemem Windows Server.

## <a name="operational-best-practices"></a>Najlepsze rozwiązanie w zakresie
Operacyjne najlepsze rozwiązania oznaczają wskazówki, które należy wykonać podczas operacji wirtualnego tablicy lub bieżące zarządzanie. Praktyki te obejmują zarządzanie określonych zadań, takich jak wykonywania kopii zapasowych, przywracania z zestawu kopii zapasowych, wykonywania pracy awaryjnej, dezaktywacja i usuwanie tablicy, monitorowania użycia systemu i kondycji i uruchomiona przed wirusami skanowania tablica wirtualnego.

### <a name="backups"></a>Tworzenie kopii zapasowych
Dane dotyczące wirtualnych tablica jest kopii zapasowej w chmurze na dwa sposoby, domyślnie automatycznego tworzenia codziennej kopii zapasowej wszystkich danych z urządzenia w 22:30 lub za pomocą ręcznej kopii zapasowej na żądanie. Domyślnie urządzenie automatycznie tworzy codzienne migawki w chmurze wszystkich danych znajdujących się na nim. Aby uzyskać więcej informacji, przejdź do [kopię zapasową tablica wirtualnego StorSimple](storsimple-virtual-array-backup.md).

Nie można zmienić częstotliwość i przechowywania skojarzonych z domyślnego tworzenia kopii zapasowych, ale możesz skonfigurować czas, w którym codzienne kopie zapasowe są inicjowane każdego dnia. Podczas konfigurowania czas rozpoczęcia automatyczne kopie zapasowe, zalecamy:

* Harmonogram kopii zapasowych dla poza godzinami szczytu. Czas rozpoczęcia tworzenia kopii zapasowej należy pokrywa się z wielu hostów we/wy.
* Zainicjuj ręczne kopii zapasowej na żądanie podczas planowania do wykonywania pracy awaryjnej urządzenia lub przed okna obsługi ochrony danych w sieci wirtualnej macierzy.

### <a name="restore"></a>Przywracanie
Można przywrócić z kopii zapasowej Ustaw na dwa sposoby: Przywracanie do innego woluminu lub udziału lub odzyskiwanie na poziomie elementu (dostępne tylko dla wirtualnych tablicy, skonfigurowany jako serwer plików). Odzyskiwanie na poziomie elementu umożliwia szczegółową odzyskiwanie plików i folderów z kopia zapasowa wszystkie udziały na urządzeniu StorSimple w chmurze. Aby uzyskać więcej informacji, przejdź do [przywrócić z kopii zapasowej](storsimple-virtual-array-clone.md).

Podczas przywracania, pamiętać o następujących wytycznych:

* Tablica wirtualnego StorSimple nie obsługuje przywracania w miejscu. Może to jednak być łatwo osiągnąć przez dwuetapowy proces: zwiększyć ilość miejsca na wirtualnej tablicy, a następnie przywróć do innego woluminu i udostępniania.
* Podczas przywracania z lokalnym woluminie, należy pamiętać, przywracania będzie operacją wymagającą dużo czasu. Jeśli wolumin może szybko przejść w tryb online, dane w dalszym ciągu można uwodniony w tle.
* Typ woluminu jest taka sama podczas procesu przywracania. Wolumin warstwowy jest przywracany do innego woluminu warstwowego i wolumin przypięty woluminu przypiętego lokalnie do innego lokalnie.
* Podczas próby przywrócenia z kopii zapasowej woluminu lub udziału ustawiona, jeśli zadanie przywracania kończy się niepowodzeniem, wolumin docelowy lub nadal można utworzyć udziału w portalu. Należy usunąć tego woluminu docelowego nieużywanych, lub udostępnianie w portalu, aby zminimalizować problemy przyszłych wynikające z tego elementu.

### <a name="failover-and-disaster-recovery"></a>Tryb failover i odzyskiwanie po awarii
Tryb failover urządzeń umożliwia migrację danych z *źródła* urządzenie w centrum danych do innego *docelowej* urządzenia znajdujące się w tej samej lub innej lokalizacji geograficznej. Tryb failover urządzeń jest przeznaczony dla wszystkich danych z urządzenia. Podczas pracy w trybie failover danych w chmurze dla urządzenia źródłowego zmiany prawa własności do tego urządzenia docelowego.

Dla macierzy wirtualnego StorSimple możesz tylko w trybie Failover do innej tablicy wirtualnych zarządzanych przez tego samego usługi Menedżer StorSimple. Tryb failover na urządzenie 8000 series lub tablica zarządzane przez inną usługę Menedżer StorSimple (niż dla urządzenia źródłowego) nie jest dozwolone. Aby dowiedzieć się więcej o kwestiach związanych z trybu failover, przejdź do [wymagania wstępne dotyczące pracy awaryjnej urządzenia](storsimple-virtual-array-failover-dr.md).

Podczas wykonywania błędu za pośrednictwem wirtualnej tablica, pamiętać o następujących czynności:

* Dla planowanego trybu failover jest zalecane do wykonania wszystkich woluminów/udziałów w trybie offline przed przejściem w tryb failover. Wykonaj instrukcje specyficzne dla systemu operacyjnego, aby wykonać pierwszy woluminów/udziałami w trybie offline na hoście, a następnie podjęcia tych w trybie offline na urządzeniu wirtualnym.
* Do pliku serwer odzyskiwania awaryjnego (DR) zaleca się dołączenie urządzenia docelowego do tej samej domeny jako źródła, aby uprawnienia udziału zostały automatycznie rozwiązane. Tylko w trybie failover na urządzeniu docelowym w tej samej domenie jest obsługiwana w tej wersji.
* Po pomyślnym ukończeniu DR urządzenia źródłowego są automatycznie usuwane. Jeśli urządzenie nie jest już dostępna, które udostępnione w systemie hosta maszyny wirtualnej nadal korzysta z zasobów. Zaleca się usunięcie tej maszyny wirtualnej w systemie hosta, aby uniknąć naliczania opłaty.
* Należy pamiętać, że nawet jeśli przejście w tryb failover zakończy się niepowodzeniem, **danych jest zawsze bezpieczne w chmurze**. Należy wziąć pod uwagę następujące trzy scenariusze, w których przejście w tryb failover nie powiodło się:
  
  * Wystąpił błąd na wczesnym etapie pracy awaryjnej, np. gdy są wykonywane wstępne sprawdzanie odzyskiwania po awarii. W takiej sytuacji urządzenia docelowego jest nadal możliwe. Możesz ponowić próbę pracy awaryjnej w tym samym urządzeniu docelowym.
  * Wystąpił błąd podczas procesu rzeczywistej pracy awaryjnej. W takim przypadku urządzenia docelowego jest oznaczony jako bezużyteczne. Należy udostępnić i skonfigurować innej docelowej tablicy wirtualnej i użyj jej w trybie failover.
  * Tryb failover zostało ukończone, po czym usunięto urządzenia źródłowego, ale urządzenie docelowe ma problemy i nie można uzyskać dostępu do żadnych danych. Dane są nadal bezpieczne w chmurze i można łatwo pobrać tworzenia innej tablicy wirtualnego, a następnie użyć go jako urządzenie docelowe do odzyskiwania po awarii.

### <a name="deactivate"></a>Dezaktywuj
Po dezaktywowaniu tablicą wirtualne StorSimple jest sever połączenia między urządzeniem i odpowiedniej usługi Menedżer StorSimple. Dezaktywacja jest **stałe** operacji i nie można cofnąć. Dezaktywowane urządzenia nie można zarejestrować w usłudze Menedżer StorSimple ponownie. Aby uzyskać więcej informacji, przejdź do [zdezaktywować i usunąć tablica wirtualnego StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Podczas dezaktywacji tablica wirtualnego należy pamiętać o następujących rozwiązań:

* Utwórz migawkę chmury wszystkie dane przed dezaktywacja urządzenia wirtualnego. Po dezaktywowaniu wirtualnego tablicy nie zostały utracone wszystkie dane urządzenia lokalnego. Tworzenie migawki chmury umożliwia odzyskiwanie danych na późniejszym etapie.
* Przed dezaktywacją tablicą wirtualnego StorSimple, upewnij się zatrzymać lub usunąć klientów i hosty, które są zależne od tego urządzenia.
* Usunąć dezaktywować urządzenie, jeśli już używasz tak, aby nie naliczania opłat.

### <a name="monitoring"></a>Monitorowanie
Aby upewnić się, że tablica wirtualne StorSimple jest w dobrej kondycji ciągłego, należy monitorować tablicy i upewnij się, otrzymywanie informacji z systemu, w tym alerty. Do monitorowania ogólnej kondycji wirtualnego tablicy, należy zaimplementować następujące najlepsze rozwiązania:

* Konfigurowanie, monitorowanie, aby śledzić użycie miejsca na dysku wirtualnego tablicy danych, a także dysk systemu operacyjnego. Jeśli z funkcją Hyper-V, służy kombinacji System Center Virtual Machine Manager (SCVMM) i System Center Operations Manager (SCOM) do monitorowania hosty wirtualizacji.
* Konfigurowanie powiadomień e-mail na wirtualnych Tablica do wysyłania alertów w niektórych poziomy użycia.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Indeks wyszukiwania i wirusów skanowanie aplikacji
Macierz wirtualnego StorSimple można automatycznie warstwy danych z lokalnego warstwy do chmury Microsoft Azure. W przypadku aplikacji, takich jak wyszukiwania indeksu lub skanowanie w poszukiwaniu wirusów do skanowania dane przechowywane na StorSimple należy zwrócić uwagę, że danych w chmurze nie uzyskać dostęp do i pobierane powrót do lokalnej warstwy.

Firma Microsoft zaleca implementować następujące najlepsze rozwiązania, konfigurując skanowania indeksu wyszukiwania lub wirusa tablica wirtualnego:

* Wyłącz wszystkie operacje automatycznie pełnego skanowania.
* Dla woluminów warstwowych skonfiguruj indeksu wyszukiwania lub wirusa skanowanie aplikacji skanowanie przyrostowe. Spowoduje to skanowanie tylko nowych danych prawdopodobnie znajdującej się na warstwie lokalnej. Dane, które jest do warstwy do chmury nie są dostępne podczas operacji przyrostowe.
* Sprawdź ustawienia i filtry wyszukiwania poprawne są skonfigurowane tak, aby pobrać skanowane zamierzone typy plików. Na przykład pliki (JPEG, GIF i TIFF) obrazów i inżynierów rysunki, nie należy przeskanować podczas odbudowywania indeksu pełnych lub przyrostowych.

Jeśli przy użyciu systemu Windows, proces indeksowania, zgodna z tymi wytycznymi:

* Nie należy używać indeksatora systemu Windows dla woluminów warstwowych, jak duże ilości danych (tabel) on odwołania z chmury Jeśli indeks musi zostać również przebudowany w często. Odbudowanie indeksu może pobrać wszystkie typy plików do indeksu ich zawartości.
* Użyj okna indeksowania procesu dla woluminów przypiętych lokalnie, ponieważ spowoduje to tylko dostęp do danych w warstwach lokalnych, aby utworzyć indeks (danych w chmurze nie będzie dostępna).

### <a name="byte-range-locking"></a>Blokowanie zakresu bajtów
Aplikacje można zablokować określony zakres bajtów w plikach. Jeśli w przypadku aplikacji, które będą zapisywane do programu StorSimple jest włączone blokowanie zakresu bajtów, następnie dodając funkcje warstw nie działa na wirtualnych macierzy. Dla warstwy do pracy wszystkie obszary dostęp do plików ma zostać odblokowana. Blokowanie zakresu bajtów nie jest obsługiwane z woluminów warstwowych w sieci wirtualnej macierzy.

Zalecane środki w celu złagodzenia blokowania zakresu bajtów obejmują:

* Wyłącz zakresu bajtów blokowania w logiki aplikacji.
* Woluminy przypięte lokalnie użyj (zamiast warstwowa) dla danych skojarzonych z tą aplikacją. Woluminów przypiętych lokalnie nie warstwy do chmury.
* Przy użyciu przypięty lokalnie woluminów z włączoną blokowania zakresu bajtów, wolumin może do trybu online przed zakończeniem przywracania. W takich przypadkach należy poczekać przywracania są kompletne.

## <a name="multiple-arrays"></a>Wiele tablic
Wiele tablic wirtualny może być konieczne można wdrożyć do konta w celu rosnącym zestaw roboczy danych, które można zostaną przeniesione na chmurze, co wpływa na wydajność urządzenia. W takich przypadkach najlepiej skali urządzeń wraz z rozwojem zestawu roboczego. Wymaga to co najmniej jedno urządzenie, które mają zostać dodane w lokalnym centrum danych. Podczas dodawania urządzenia, można:

* Podziel bieżącego zestawu danych.
* Wdrażania nowych obciążeń do nowych urządzeń.
* W przypadku wdrażania wielu wirtualnych tablic, zaleca się z równoważeniem obciążenia perspektywy, rozpowszechniają tablicy innej funkcji hypervisor hostów.
* Wiele wirtualnych tablic (jeśli jest skonfigurowany jako serwer plików lub serwera iSCSI) można wdrożyć w Namespace rozproszonego systemu plików. Aby uzyskać szczegółowy opis kroków, przejdź do [rozproszonych pliku systemu Namespace rozwiązania z Podręcznik wdrażania magazynu chmury hybrydowej](https://www.microsoft.com/download/details.aspx?id=45507). Obecnie rozproszonej replikacji systemu plików nie jest zalecane do użycia z wirtualnych tablicy. 

## <a name="see-also"></a>Zobacz także
Dowiedz się, jak [administrowania tablica wirtualnego StorSimple](storsimple-virtual-array-manager-service-administration.md) za pośrednictwem usługi Menedżer StorSimple.

