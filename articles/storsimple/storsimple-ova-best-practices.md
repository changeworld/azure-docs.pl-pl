---
title: Najlepsze rozwiązania dotyczące macierzy wirtualnej StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano najlepsze rozwiązania dotyczące wdrażania i zarządzania nimi macierzy wirtualnej StorSimple.
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
ms.date: 11/08/2018
ms.author: alkohli
ms.openlocfilehash: b8e9f12a549f71971c2da3b9865f6a74dad58f61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630142"
---
# <a name="storsimple-virtual-array-best-practices"></a>Najlepsze rozwiązania StorSimple Virtual Array
## <a name="overview"></a>Omówienie
Microsoft Azure StorSimple Virtual Array to zintegrowane rozwiązanie do magazynowania, który zarządza zadaniami magazynowania między urządzenia wirtualnego środowiska lokalnego w funkcji hypervisor i Magazyn w chmurze Microsoft Azure. StorSimple Virtual Array to wydajne, ekonomiczne zamiast fizycznej macierzy magazynowych z serii 8000. Macierz wirtualną można uruchomić w istniejącej infrastrukturze funkcji hypervisor, obsługuje zarówno protokołom iSCSI i SMB i dobrze nadaje się dla zdalnych biurach/oddziałach scenariuszach dotyczących biura. Aby uzyskać więcej informacji na temat rozwiązania StorSimple, przejdź do [Przegląd usługi Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

W tym artykule opisano najlepsze rozwiązania, które są implementowane podczas początkowej konfiguracji, wdrażania i zarządzania macierzy wirtualnej StorSimple. Następujące najlepsze rozwiązania zamieszczono wytyczne zweryfikowanych Konfiguracja i zarządzanie macierz wirtualna. W tym artykule jest przeznaczona do administratorów IT, którzy wdrażanie i Zarządzanie macierzami wirtualnymi w swoich centrach danych.

Zalecamy okresowe analizy najlepszych rozwiązań ułatwiających, upewnij się, że urządzenie jest nadal w zakresie zgodności podczas wprowadzania zmian do przepływu konfiguracji lub operacji. Należy napotkasz jakiekolwiek problemy podczas implementowania tych najlepszych rozwiązań na macierz wirtualna [skontaktuj się z Microsoft Support](storsimple-virtual-array-log-support-ticket.md) uzyskać pomoc.

## <a name="configuration-best-practices"></a>Najlepszymi rozwiązaniami konfiguracyjnymi
Następujące najlepsze rozwiązania obejmują wskazówki, które muszą występować podczas początkowej konfiguracji i wdrażania macierzy wirtualnych. Następujące najlepsze rozwiązania obejmują te związane z aprowizacji maszyny wirtualnej, ustawienia zasad grupy, zmiany rozmiaru, konfigurowanie sieci, konfigurowania kont magazynu i tworzenie udziałów i woluminów dla macierzy wirtualnej. 

### <a name="provisioning"></a>Inicjowanie obsługi
Macierz wirtualna StorSimple jest maszynę wirtualną (VM), którego obsługę zainicjowano na funkcji hypervisor (Hyper-V lub VMware) serwera hosta. Podczas aprowizowania maszyny wirtualnej, upewnij się, że hosta jest w stanie dedykować wystarczające zasoby. Aby uzyskać więcej informacji, przejdź do [minimalnych wymagań dotyczących zasobów](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) do aprowizowania macierzy.

Podczas aprowizowania macierzy wirtualnej, należy zaimplementować następujące najlepsze rozwiązania:

|  | Funkcja Hyper-V | VMware |
| --- | --- | --- |
| **Typ maszyny wirtualnej** |**2. generacji** maszynę Wirtualną do użytku z systemem Windows Server 2012 lub nowszym i *vhdx* obrazu. <br></br> **Generacja 1** maszynę Wirtualną do użytku z systemem Windows Server 2008 lub nowszym i *VHD* obrazu. |Użyj maszyn wirtualnych w wersji 8, korzystając z *.vmdk* obrazu. |
| **Typ pamięci** |Należy skonfigurować jako **pamięci statycznej**. <br></br> Nie używaj **pamięci dynamicznej** opcji. | |
| **Typ dysku danych** |Aprowizowanie jako **dynamicznie powiększających się**.<br></br> **Ustalony rozmiar** zajmuje dużo czasu. <br></br> Nie używaj **różnicowych** opcji. |Użyj **alokowanych aprowizowanie** opcji. |
| **Modyfikowanie dysku danych** |Rozszerzenie lub zmniejszania nie jest dozwolone. Próby takiego przydzielenia spowoduje utratę danych lokalnych na urządzeniu. |Rozszerzenie lub zmniejszania nie jest dozwolone. Próby takiego przydzielenia spowoduje utratę danych lokalnych na urządzeniu. |

### <a name="sizing"></a>Ustalanie rozmiaru
Podczas zmiany rozmiaru rozwiązania StorSimple Virtual Array, należy wziąć pod uwagę następujące czynniki:

* Zarezerwowane miejsce lokalne dla woluminy lub udziały. Około 12% wolnego miejsca jest zarezerwowana w warstwie lokalnego dla każdego elastycznie warstwowego woluminu lub udziału. Około 10% wolnego miejsca, również jest zarezerwowana dla woluminu przypiętego lokalnie dla systemu plików.
* Obciążenie migawki. Około 15% miejsca w warstwie lokalnej jest zarezerwowana dla migawki.
* Wymagany dla przywracania. Jeśli wykonanie tych czynności przywracania jako nową operację, ustalania rozmiaru powinien uwzględniać miejsce wymagane do przywracania. Przywracania odbywa się na udziale lub woluminie z tej samej wielkości.
* Niektóre buforu powinna zostać przydzielona dla dowolnego nieoczekiwanego zwiększania.

Na podstawie poprzedniego czynników, wymagania w zakresie rozmiaru może być reprezentowany przez poniższego równania:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Poniższe przykłady ilustrują, jak dostosować rozmiar macierzy wirtualnej zgodnie z wymaganiami.

#### <a name="example-1"></a>Przykład 1:
Macierz wirtualna chcesz mieć możliwość

* aprowizuj 2 TB warstwowego woluminu lub udziału.
* aprowizuj 1 TB pojemności warstwowego woluminu lub udziału.
* aprowizuj 300 GB woluminu przypiętego lokalnie lub w udziale.

Na poprzednim woluminy lub udziały Daj nam obliczyć wymagania dotyczące miejsca na warstwie lokalnej.

Po pierwsze dla każdego woluminu/udziału warstwowego zarezerwowane miejsce lokalne będzie równa 12% rozmiaru woluminu/udziału. Dla woluminów przypiętych lokalnie i udostępniania zarezerwowane miejsce lokalne jest 10% rozmiaru woluminu przypiętego lokalnie i udostępniania (oprócz aprowizowanego rozmiaru). W tym przykładzie należy

* Zarezerwowane miejsce lokalne 240 GB (do 2 TB woluminu/udziału warstwowego)
* Zarezerwowane miejsce lokalne 120 GB (do 1 TB pojemności woluminu/udziału warstwowego)
* 330 GB dla woluminu przypiętego lokalnie lub udziału (dodanie zarezerwowane miejsce lokalne 10% rozmiaru 300 GB aprowizacji)

Całkowita ilość miejsca wymaganego w warstwie lokalnej do tej pory jest: 240 GB + 120 GB + 330 GB = 690 GB.

Po drugie potrzebujemy co najmniej taką ilość miejsca na warstwie lokalnej jako największych pojedynczego rezerwacji. Ten dodatkowy kwota jest używana w przypadku, gdy trzeba przywrócić z migawki w chmurze. W tym przykładzie największych zarezerwowane miejsce lokalne jest 330 GB (w tym rezerwacji dla systemu plików), dzięki czemu można będzie dodać je do 690 GB: 690 GB + 330 GB = 1020 GB.
Jeśli wykonane kolejne dodatkowe operacje przywracania, firma Microsoft zawsze bezpłatne miejsce z poprzedniej operacji przywracania.

Po trzecie potrzebna 15%, całkowita miejsca lokalnego do tej pory, do przechowywania migawki lokalne, aby tylko 85% firm z jej jest dostępna. W tym przykładzie byłoby wokół 1020 GB = 0,85&ast;aprowizowane dane na dysku TB. Tak, będzie dysku aprowizowane dane (1020&ast;(1/0,85)) = 1200 GB = 1,20 TB ~ 1,25 TB (zaokrąglania do najbliższej kwartyl)

Wyprowadzenie nieoczekiwanego zwiększania i nowe operacje przywracania, należy zainicjować obsługę administracyjną dysku lokalnego wokół 1,25 — 1,5 TB.

> [!NOTE]
> Zalecamy również, że dysk lokalny jest alokowany elastycznie. To zalecenie wynika miejsca przywracania jest potrzebny tylko wtedy, gdy chcesz przywrócić dane, które są starsze niż pięć dni. Odzyskiwanie na poziomie elementu umożliwia przywracanie danych w ciągu ostatnich pięciu dni bez konieczności dodatkowego miejsca do przywrócenia.


#### <a name="example-2"></a>Przykład 2:
Macierz wirtualna chcesz mieć możliwość

* w przypadku alokowania 2 TB warstwowego woluminu
* alokowania woluminu przypiętego lokalnie 300 GB

Oparte na 12% rezerwacji lokalnego miejsca warstwowych woluminów/udziałów oraz 10% lokalnie przypiętych woluminów/udziałów, potrzebujemy

* Zarezerwowane miejsce lokalne 240 GB (do 2 TB warstwowego woluminu/udziału)
* 330 GB dla woluminu przypiętego lokalnie lub udziału (dodanie 10% zarezerwowane miejsce lokalne miejsce o rozmiarze 300 GB obsługiwana administracyjnie)

Całkowita ilość miejsca wymaganego w warstwie lokalnej jest: 240 GB + 330 GB = 570 GB

Minimalna lokalne miejsce wymagane do przywrócenia jest 330 GB.

15%, całkowita dysku służy do przechowywania migawek, tak aby tylko 0,85 jest dostępna. Rozmiar dysku jest więc (900&ast;(1/0,85)) = 1.06 TB ~ 1,25 TB (zaokrąglania do najbliższej kwartyl)

Uwzględniając wszelkie nieoczekiwany wzrost, można udostępnić dysk lokalny 1,25 — 1,5 TB.

### <a name="group-policy"></a>Zasady grupy
Zasady grupy to infrastruktura umożliwiająca do zaimplementowania konfiguracji specyficznych dla użytkowników i komputerów. Ustawienia zasad grupy są zawarte w obiektach zasad grupy (GPO), które są połączone z następujących kontenerach usługi Active Directory Domain Services (AD DS): lokacje, domeny i jednostki organizacyjne (OU). 

Jeśli macierz wirtualna jest przyłączony do domeny, można zastosować obiekty zasad grupy do niej. Te obiekty zasad grupy można zainstalować aplikacji, takich jak oprogramowanie antywirusowe, które może niekorzystnie wpłynąć na działanie macierzy wirtualnej StorSimple.

Dlatego zaleca się, że możesz:

* Upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory.
* Upewnij się, że nie obiekty zasad grupy (GPO) są stosowane do macierz wirtualna. Można zablokować dziedziczenie, aby upewnić się, że macierzy wirtualnej (węzeł podrzędny) nie automatycznie dziedziczy żadnych obiektów zasad grupy z obiektu nadrzędnego. Aby uzyskać więcej informacji, przejdź do [zablokować dziedziczenie](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Networking
Konfiguracja sieci dla macierzy wirtualnych odbywa się za pośrednictwem lokalnego Interfejsu w przeglądarce. Interfejs sieci wirtualnej jest włączone za pomocą funkcji hypervisor, w którym zainicjowano macierzy wirtualnej. Użyj [ustawienia sieciowe](storsimple-virtual-array-deploy3-fs-setup.md) strony, aby skonfigurować adres IP interfejsu sieci wirtualnej, podsieci i bramę.  Można również skonfigurować podstawowego i pomocniczego serwera DNS, ustawienia czasu i opcjonalne ustawienia serwera proxy dla Twojego urządzenia. W większości konfiguracji sieci jest to jednorazowa konfiguracja. Przegląd [StorSimple wymagania sieciowe](storsimple-ova-system-requirements.md#networking-requirements) przed wdrożeniem macierzy wirtualnej.

Podczas wdrażania macierz wirtualna, zaleca się, aby wykonać te najlepsze rozwiązania:

* Upewnij się, że sieci, w której wdrożono zawsze macierz wirtualna jest w stanie dedykować 5 MB/s przepustowości Internetu (lub więcej).
  
  * Konieczność przepustowości Internetu różni się w zależności od charakterystyki i częstotliwość zmian danych.
  * Zmiany danych, który może być obsługiwany jest wprost proporcjonalna do przepustowości Twojego połączenia internetowego. Na przykład gdy wykonywanie kopii zapasowej 5 MB/s przepustowości może obsłużyć zmian danych w około 18 GB w 8 godzin. Cztery razy większe wartości przepustowości (20 MB/s) może obsługiwać cztery razy więcej zmian danych (72 GB).
* Upewnij się, że łączność z Internetem jest zawsze dostępna. Sporadyczne i nieprzewidywalne połączeń internetowych na urządzeniach może spowodować utratę dostępu do danych w chmurze i może skutkować nieobsługiwaną konfigurację.
* Jeśli planujesz wdrożyć urządzenie jako serwera iSCSI:
  
  * Firma Microsoft zaleca, aby wyłączyć **Uzyskaj adres IP automatycznie** opcji (DHCP).
  * Konfigurowanie statycznych adresów IP. Skonfiguruj podstawowy i pomocniczy serwer DNS.
  * Jeśli Definiowanie wiele interfejsów sieciowych na wirtualnej tablicy, tylko pierwszy interfejs sieciowy (domyślnie ten interfejs jest **Ethernet**) mogą dotrzeć do chmury. Aby kontrolować typ ruchu, możesz utworzyć wiele interfejsów sieci wirtualnej na macierz wirtualna (Konfiguracja serwera jako serwera iSCSI) i połącz te interfejsy do różnych podsieci.
* Aby ograniczać pasmo tylko w chmurze (wykorzystywane przez macierz wirtualna), skonfigurować ograniczanie na routerze lub zapory. Jeśli zdefiniujesz jest ograniczenie dostępu z funkcji hypervisor, będzie ograniczenie przepustowości wszystkich protokołów, w tym iSCSI i SMB zamiast po prostu przepustowość chmury.
* Upewnij się że synchronizacja czasu jest włączone w funkcji hypervisor. Jeśli przy użyciu funkcji Hyper-V, wybierz macierz wirtualna w Menedżerze funkcji Hyper-V, przejdź do strony **ustawienia &gt; usług Integration Services**i upewnij się, że **synchronizacja czasu** jest zaznaczone.

### <a name="storage-accounts"></a>Konta magazynu
Macierz wirtualną StorSimple można skojarzyć z jednego konta magazynu. To konto magazynu może być konto usługi storage automatycznie generowanych, konto w tej samej subskrypcji co usługa, lub konto magazynu powiązane z innej subskrypcji. Aby uzyskać więcej informacji, zobacz instrukcje [Zarządzanie kontami magazynu na macierz wirtualna](storsimple-virtual-array-manage-storage-accounts.md).

Używaj poniższych zaleceń dla konta magazynu skojarzone z macierz wirtualna.

* Podczas łączenia wielu macierzy wirtualnych za pomocą jednego konta magazynu, wziąć pod uwagę maksymalną pojemność (64 TB) dla macierzy wirtualnej i maksymalny rozmiar (500 TB) dla konta magazynu. To ogranicza liczbę pełny macierzami wirtualnymi, które mogą być skojarzone z tym kontem magazynu, do około 7.
* Podczas tworzenia nowego konta magazynu
  
  * Firma Microsoft zaleca utworzenie go w regionie najbliżej office zdalnych biurach/oddziałach, w którym rozwiązania StorSimple Virtual Array jest wdrażany w celu zminimalizowania opóźnienia.
  * Mieć na uwadze, że nie można przenieść na koncie magazynu w różnych regionach. Ponadto nie można przenieść usługi w subskrypcjach.
  * Użyj konta magazynu, który implementuje nadmiarowość między centrami danych. Magazyn geograficznie nadmiarowy (GRS), Magazyn Strefowo nadmiarowy (ZRS) i Magazyn lokalnie nadmiarowy (LRS) są obsługiwane do użytku z programem macierz wirtualna. Aby uzyskać więcej informacji na temat różnych typów kont magazynu, przejdź do [replikacji usługi Azure storage](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Udziały i woluminy
Dla rozwiązania StorSimple Virtual Array udziałów można udostępnić, gdy jest on skonfigurowany jako serwer plików i woluminami, w przypadku skonfigurowania serwera jako serwera iSCSI. Najlepsze rozwiązania dotyczące tworzenia, udziały i woluminy są związane z rozmiar i typ skonfigurowany.

#### <a name="volumeshare-size"></a>Rozmiar woluminu/udziału
Na macierz wirtualna można alokować udziałów, gdy jest on skonfigurowany jako serwer plików i woluminami, w przypadku skonfigurowania serwera jako serwera iSCSI. Najlepsze rozwiązania dotyczące tworzenia, udziały i woluminy odnoszą się do rozmiaru i typu skonfigurowane. 

Należy pamiętać, poniższe najlepsze rozwiązania podczas aprowizowania udziałów lub woluminów na urządzeniu wirtualnym.

* Rozmiary plików aprowizowanego rozmiaru udziału warstwowego może wpłynąć na wydajność obsługi warstw. Praca z dużymi plikami, może spowodować wolne warstwy do zewnątrz. Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż 3% rozmiaru udziału.
* Maksymalny wynoszący 16 woluminów/udziałów, mogą być tworzone w ramach macierzy wirtualnej. Limity rozmiaru lokalnie przypięty i warstwowych woluminów/udziałów, zawsze można znaleźć [limity macierzy wirtualnej StorSimple](storsimple-ova-limits.md).
* Podczas tworzenia woluminu należy wziąć pod uwagę użycie oczekiwanych danych, a także przyszły rozwój. Wolumin nie może zostać rozszerzona później.
* Po utworzeniu woluminu nie można zmniejszyć rozmiaru woluminu na StorSimple.
* Podczas zapisywania woluminu warstwowego na StorSimple, gdy wolumin danych osiągnie określony próg (względem lokalne miejsce zarezerwowane dla woluminu), we/wy jest ograniczane. Zapis do tego woluminu w dalszym ciągu spowalnia we/wy znacznie. Chociaż można zapisywać na wolumin warstwowy poza jego zaprowizowaną pojemnością (Firma Microsoft nie aktywnie uniemożliwia użytkownikowi zapis poza zaprowizowaną pojemnością), zostanie wyświetlony powiadomień o alertach, że możesz mieć zażądałeś. Gdy zostanie wyświetlony alert, należy bezwzględnie podjąć środki zaradcze, takie jak usuwanie danych woluminu (rozszerzenie woluminu nie jest obecnie obsługiwane).
* Dla przypadków użycia odzyskiwania po awarii liczba udziałów dopuszczalny rozmiar/woluminów 16 i maksymalna liczba udziałów/woluminów, które mogą być przetwarzane równolegle jest również 16, liczba woluminów/udziałów nie ma żadnego wpływu na cel punktu odzyskiwania i docelowego czasu odzyskiwania.

#### <a name="volumeshare-type"></a>Typ woluminu/udziału
StorSimple obsługuje dwa typy wolumin/udział na podstawie użycia: lokalnie przypięte i warstwowego. Lokalnie przypiętych woluminów/udziałów są aprowizowane, natomiast woluminów/udziałów warstwowy jest alokowany elastycznie. Nie można przekonwertować wolumin przypięty lokalnie/udział do warstwowe lub *odwrotnie* po utworzeniu.

Firma Microsoft zaleca zaimplementować następujące najlepsze rozwiązania, podczas konfigurowania usługi StorSimple woluminów/udziałów:

* Określ typ woluminu na podstawie obciążeń, które mają zostać wdrożone przed przystąpieniem do tworzenia woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji danych (nawet podczas awarii chmury) i które wymagają chmury niskie opóźnienia, należy użyć woluminów przypiętych lokalnie. Po utworzeniu woluminu na macierz wirtualna nie można zmienić typu woluminu z lokalnie przypięte do warstwowe lub *odwrotnie*. Na przykład tworzenia woluminów przypiętych lokalnie, w przypadku wdrażania obciążeń programu SQL lub obciążeń hostingu maszyn wirtualnych (VM); Użyj woluminy warstwowe na potrzeby obciążeń udziału plików.


#### <a name="volume-format"></a>Formatowanie woluminu
Po utworzeniu woluminów StorSimple na serwerze iSCSI, należy zainicjować, zainstalować i sformatować woluminy. Ta operacja odbywa się na hoście podłączone do urządzenia StorSimple. Następujące najlepsze rozwiązania są zalecane, gdy zainstalowanie i formatowanie woluminów StorSimple hosta.

* We wszystkich woluminach StorSimple, należy wykonać szybkie formatowanie.
* W przypadku formatowania woluminu StorSimple, należy użyć rozmiaru jednostki alokacji (Australia) 64 KB (wartość domyślna to 4 KB). 64 KB AUS opiera się na testowania we własnym zakresie dla typowych obciążeń StorSimple i innych obciążeń.
* Korzystając z rozwiązania StorSimple Virtual Array, skonfigurowany jako serwer iSCSI, nie używaj woluminy łączone ani dysków dynamicznych te woluminy lub dyski nie są obsługiwane przez rozwiązanie StorSimple.

#### <a name="share-access"></a>Dostęp do udziału
Podczas tworzenia udziałów na serwerze plików macierzy wirtualnej, należy przestrzegać następujących wytycznych:

* Podczas tworzenia udziału, należy przypisać grupę użytkowników jako administrator udziału zamiast pojedynczego użytkownika.
* Po utworzeniu udziału, edytując akcji za pomocą Eksploratora Windows, można zarządzać uprawnienia systemu plików NTFS.

#### <a name="volume-access"></a>Dostęp do woluminu
Podczas konfigurowania rozwiązania StorSimple Virtual Array woluminy iSCSI należy koniecznie kontroli dostępu, wszędzie tam, gdzie to konieczne. Aby określić, które serwery hosta mogą uzyskiwać dostęp do woluminów, tworzenie i kojarzenie rekordy kontroli dostępu (rekordów Acr) przy użyciu woluminów StorSimple.

Podczas konfigurowania rekordów Acr dla woluminów StorSimple, należy użyć następujących najlepszych rozwiązań:

* Zawsze należy skojarzyć ACR co najmniej jeden z woluminu.

* Podczas przypisywania więcej niż jednego rekordu ACR woluminu, upewnij się, że wolumin nie jest uwidaczniana w taki sposób, w którym jest jednocześnie dostępny przez więcej niż jeden host nieklastrowany. Jeśli przypisano wiele rekordów Acr woluminu, ostrzeżenie pojawia się należy przejrzeć konfigurację.

### <a name="data-security-and-encryption"></a>Bezpieczeństwo i szyfrowanie danych
Rozwiązania StorSimple Virtual Array ma funkcji zabezpieczeń i szyfrowania danych, które zapewnią, poufności i integralności danych. Korzystając z tych funkcji, zalecane jest, należy wykonać następujące najlepsze rozwiązania: 

* Zdefiniuj klucz szyfrowania magazynu w chmurze do generowania szyfrowania AES-256, przed wysłaniem danych z macierz wirtualna w chmurze. Ten klucz nie jest wymagane, jeśli dane są szyfrowane rozpoczynać się. Klucz może być wygenerowany i bezpieczne, przy użyciu systemu zarządzania kluczami, takich jak [usługi Azure key vault](../key-vault/key-vault-whatis.md).
* Podczas konfigurowania konta magazynu przy użyciu usługi StorSimple Manager, upewnij się, że Włącz tryb SSL utworzyć bezpieczny kanał komunikacji sieciowej między chmurą a urządzeniem StorSimple.
* Ponownie generując klucze konta magazynu (uzyskując dostęp do usługi Azure Storage) okresowo dla zmiany w celu uzyskania dostępu do konta na podstawie zmienionych listy administratorów.
* Dane na macierz wirtualna jest skompresowany i deduplikowany przed wysłaniem go do platformy Azure. Nie zaleca się przy użyciu usługi roli deduplikacji danych na hoście z systemem Windows Server.

## <a name="operational-best-practices"></a>Najlepsze rozwiązanie w zakresie
Najlepsze rozwiązanie w zakresie znajdują się wytyczne, które dotyczą sytuacji, w operacji macierzy wirtualnej lub bieżące zarządzanie. Praktyki te obejmują zadania zarządzania, takie jak wykonywania kopii zapasowych, przywracania z zestawu kopii zapasowych, wykonanie przejścia w tryb failover, dezaktywowanie i usuwanie tablicy, monitorowania użycia systemu i kondycji i uruchamianie wirusów skanowania macierz wirtualna.

### <a name="backups"></a>Tworzenie kopii zapasowych
Dane na macierz wirtualna jest wykonywana kopia zapasowa w chmurze na dwa sposoby, domyślny automatycznego tworzenia codziennej kopii zapasowej całego urządzenia o 22:30 lub za pomocą ręcznego tworzenia kopii zapasowej na żądanie. Domyślnie urządzenie automatycznie tworzy dzienne migawki w chmurze wszystkich danych znajdujących się na nim. Aby uzyskać więcej informacji, przejdź do [kopii zapasowej rozwiązania StorSimple Virtual Array](storsimple-virtual-array-backup.md).

Nie można zmienić częstotliwość i przechowywania skojarzonych z domyślnego tworzenia kopii zapasowych, ale można skonfigurować czasu, jaką codzienne kopie zapasowe są inicjowane każdego dnia. Podczas konfigurowania czas rozpoczęcia dla zautomatyzowanych kopii zapasowych, zalecamy:

* Planowanie tworzenia kopii zapasowych poza godzinami pracy. Czas rozpoczęcia tworzenia kopii zapasowych powinny pokrywa się z hostem wiele operacji We/Wy.
* Podczas planowania i wykonywania pracy awaryjnej urządzenia lub przed okna obsługi, aby chronić dane na macierz wirtualna, należy zainicjować ręcznego tworzenia kopii zapasowej na żądanie.

### <a name="restore"></a>Przywracanie
Można przywrócić z kopii zapasowej Ustaw na dwa sposoby: Przywracanie do innego woluminu lub udziału lub odzyskiwanie na poziomie elementu (dostępne tylko dla macierzy wirtualnej skonfigurowany jako serwer plików). Odzyskiwanie na poziomie elementu umożliwia to szczegółowe odzyskiwanie plików i folderów z kopia zapasowa wszystkich akcji na urządzeniu StorSimple w chmurze. Aby uzyskać więcej informacji, przejdź do [przywracania z kopii zapasowej](storsimple-virtual-array-clone.md).

Podczas przywracania pamiętać o następujących wytycznych:

* Rozwiązania StorSimple Virtual Array nie obsługuje przywracania w miejscu. Jednak można to łatwo osiągnąć, dwuetapowy proces: zwiększyć ilość miejsca na wirtualnej macierz, a następnie przywróć do innego woluminu/udziału.
* W przypadku przywracania z lokalnym woluminie, należy wziąć pod uwagę przywracania będzie długotrwałej operacji. Chociaż wolumin może szybko przejść w tryb online, dane w dalszym ciągu być uwodniony w tle.
* Typ woluminu pozostaje taka sama podczas procesu przywracania. Wolumin warstwowy jest przywracana na inny wolumin warstwowy, i woluminu przypiętego lokalnie do innego lokalnie przypiętych woluminów.
* Podczas próby przywracania z kopii zapasowej woluminu lub udziału ustawiony, w przypadku niepowodzenia zadania przywracania woluminu docelowego, lub nadal można utworzyć udziału w portalu. Należy usunąć ten wolumin docelowy nieużywane, lub udostępnianie w portalu, aby zminimalizować problemy w przyszłości wynikające z tego elementu.

### <a name="failover-and-disaster-recovery"></a>Tryb failover i odzyskiwanie po awarii
Przejścia w tryb failover urządzenia umożliwia migrację danych z *źródła* urządzenia w centrum danych do innego *docelowej* urządzenia znajdujące się w tej samej lub innej lokalizacji geograficznej. Tryb failover urządzenia dotyczy całego urządzenia. Podczas pracy w trybie failover danych w chmurze dla urządzenia źródłowego zmiany prawa własności do tego urządzenia docelowego.

Dla rozwiązania StorSimple Virtual Array tylko może zostać przeniesiony do innej macierzy wirtualnej zarządzane przez tę samą usługę Menedżer StorSimple. Tryb failover, aby urządzenia z serii 8000 lub tablica zarządzana przez inną usługę StorSimple Manager (niż ta, do urządzenia źródłowego) nie jest dozwolone. Aby dowiedzieć się więcej o kwestiach związanych z trybu failover, przejdź do [wymagania wstępne dotyczące pracy w trybie failover urządzenia](storsimple-virtual-array-failover-dr.md).

Podczas wykonywania awarii za pośrednictwem macierz wirtualna, pamiętać o następujących czynności:

* W przypadku planowanego trybu failover jest zalecanym najlepszym rozwiązaniem, aby zająć wszystkich woluminów/udziałów w trybie offline przed przejściem w tryb failover. Wykonaj instrukcje specyficzne dla systemu operacyjnego, aby wykonać pierwszy woluminów/udziałów w trybie offline na hoście, a następnie podjęcia tych w trybie offline na urządzeniu wirtualnym.
* Plik serwer odzyskiwania po awarii (DR) zaleca się przyłączyć urządzenie docelowe do tej samej domenie co źródłowy, tak, aby uprawnień udziału są automatycznie rozwiązywane. Tylko trybu failover na urządzeniu docelowym w tej samej domenie jest obsługiwana w tej wersji.
* Po pomyślnym ukończeniu odzyskiwania po awarii urządzenie źródłowe jest automatycznie usuwany. Jeśli urządzenie nie jest już dostępna, maszyna wirtualna aprowizowana przez Ciebie w systemie hosta jest nadal zużywają zasoby. Firma Microsoft zaleca, Usuń tę maszynę wirtualną z systemu hosta, aby uniemożliwić naliczane opłaty.
* Należy pamiętać, że nawet jeśli przełączenie w tryb failover kończy się niepowodzeniem, **dane są zawsze bezpieczne w chmurze**. Należy wziąć pod uwagę następujące trzy scenariusze, w których tryb failover nie powiodło się:
  
  * Wystąpił błąd na wczesnym etapie pracy awaryjnej, np. gdy są wykonywane wstępne kontrole odzyskiwania po awarii. W takiej sytuacji urządzenia docelowego jest nadal można używać. Możesz ponowić próbę pracy w trybie failover na tym samym urządzeniu docelowym.
  * Wystąpił błąd podczas procesu rzeczywistego uruchamiania trybu failover. W takim przypadku urządzenie docelowe jest oznaczony jako nienadające się do użytku. Należy aprowizować i skonfigurować innej macierzy wirtualnej docelowego i używać go w tryb failover.
  * Przełączenie w tryb failover zostało ukończone, po czym usunięto urządzenie źródłowe, ale urządzenie docelowe ma problemy, a nie masz dostępu do żadnych danych. Dane są nadal bezpiecznego w chmurze i można łatwo pobrać tworząc innej macierzy wirtualnej, a następnie użycie go jako urządzenie docelowe do odzyskiwania po awarii.

### <a name="deactivate"></a>Dezaktywuj
Po dezaktywowaniu rozwiązania StorSimple Virtual Array jest sever połączenia między urządzeniem i odpowiednie usługi StorSimple Manager. Dezaktywacja jest **stałe** operacji można cofnąć. Dezaktywowanego urządzenia nie można ponownie zarejestrować w usłudze StorSimple Manager. Aby uzyskać więcej informacji, przejdź do [Dezaktywacja i usuwanie rozwiązania StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Podczas dezaktywacji macierz wirtualna, należy pamiętać o następujących najlepszych rozwiązań:

* Migawkę w chmurze wszystkie dane przed dezaktywacji urządzenia wirtualnego. Po dezaktywowaniu macierzy wirtualnej wszystkie dane lokalne urządzenia zostaną utracone. Wykonanie migawki w chmurze będzie można odzyskać dane na późniejszym etapie.
* Przed dezaktywacją rozwiązania StorSimple Virtual Array, upewnij się zatrzymać lub usunąć klientów i hosty, które są zależne od tego urządzenia.
* Usuń dezaktywowanego urządzenia, jeśli już używasz, aby go nie naliczane opłaty.

### <a name="monitoring"></a>Monitorowanie
Aby upewnić się, że rozwiązania StorSimple Virtual Array jest w dobrej kondycji ciągłe, należy monitorować tablicy i upewnij się, otrzymywać informacje z systemu, w tym alerty. Do monitorowania ogólnej kondycji macierzy wirtualnej, należy zaimplementować następujące najlepsze rozwiązania:

* Konfigurowanie, monitorowanie, aby śledzić użycie miejsca na dysku danych macierzy wirtualnej, a także dysku systemu operacyjnego. Jeśli z funkcją Hyper-V, umożliwia połączenie programu System Center Virtual Machine Manager (SCVMM) i System Center Operations Manager monitoruje hosty wirtualizacji.
* Konfigurowanie powiadomień e-mail na macierz wirtualna do wysyłania alertów konkretne poziomy użycia.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Indeks wyszukiwania i wirus skanowania aplikacji
Macierz wirtualną StorSimple można automatycznie obsługiwać warstw danych w warstwie lokalnej do chmury Microsoft Azure. W przypadku aplikacji takich jak indeks wyszukiwania lub skanowanie w poszukiwaniu wirusów do skanowania danych przechowywanych na StorSimple musisz zajmie się, że danych w chmurze nie uzyskać dostęp do i ponownie pobrane do lokalnego warstwy.

Firma Microsoft zaleca zaimplementować następujące najlepsze rozwiązania, konfigurując skanowania indeksu wyszukiwania lub działania wirusa macierz wirtualna:

* Wyłącz wszystkie operacje automatycznie skonfigurowana pełnego skanowania.
* Dla woluminy warstwowe należy skonfigurować indeksu wyszukiwania lub działania wirusa skanowania aplikacji skanować przyrostowe. Spowoduje to skanowanie tylko nowe dane prawdopodobnie znajdującej się na warstwie lokalnej. Dane, które jest warstwowe w chmurze nie są dostępne podczas operacji przyrostowe.
* Upewnij się, ustawienia i filtry wyszukiwania poprawne są skonfigurowane tak, aby uzyskać skanowane zamierzony typów plików. Na przykład obraz plików (JPEG, GIF i TIFF) i inżynierów rysunki nie powinny być przeskanowane podczas odbudowywanie indeksu pełnych lub przyrostowych.

Jeśli używasz Windows indeksowania procesu, należy przestrzegać następujących wytycznych:

* Nie należy używać indeksatora Windows dla woluminów warstwowych, zgodnie z jego przypomina dużych ilości danych (TB) w chmurze Jeśli indeks wymaga odbudowania często. Odbudowanie indeksu będzie pobierać wszystkie typy plików do indeksowania ich zawartości.
* Za pomocą Windows indeksowania procesu dla woluminów przypiętych lokalnie, jak to tylko dostępu do danych w warstwach lokalnych, aby utworzyć indeks (danych w chmurze nie będą dostępne).

### <a name="byte-range-locking"></a>Blokowanie zakresu bajtów
Aplikacje można zablokować określony zakres bajtów w plikach. Jeśli w aplikacji, które będą zapisywane do usługi StorSimple jest włączone blokowanie zakresu bajtów, następnie obsługi warstw nie działa na macierz wirtualna. Warstw do pracy wszystkich obszarów narzędzia pliki dostępne powinna być odblokowane. Blokowanie zakres bajtów jest nieobsługiwane w przypadku woluminów warstwowych na macierz wirtualna.

Zalecanych środków w celu złagodzenia, blokowanie zakresu bajtów obejmują:

* Wyłącz zakresu bajtów blokowania w logice aplikacji.
* Użyj lokalnie przypięte woluminy (zamiast warstwowa) dla danych skojarzonych z tą aplikacją. Woluminy przypięte lokalnie nie warstwy do chmury.
* Przy użyciu lokalnie przypięte woluminy z blokowaniem zakresu bajtów włączone, wolumin może przejdzie w tryb online przed zakończeniem przywracania. W tych przypadkach należy poczekać do przywrócenia zakończenie.

## <a name="multiple-arrays"></a>Wiele tablic
Wiele macierzy wirtualnej może być konieczne do wdrożenia dla konta pracy rosnący zbiór danych, które można zostaną przeniesione do chmury, co wpływa na wydajność urządzenia. W tych przypadkach najlepiej na urządzeniach skalowania wraz ze wzrostem natężenia zestaw roboczy. Wymaga to co najmniej jedno urządzenie, które mają zostać dodane w lokalnym centrum danych. Podczas dodawania urządzeń, można wykonać następujące akcje:

* Podziel bieżący zestaw danych.
* Wdróż nowe obciążenia nowych urządzeń.
* Jeśli wdrożenie wielu macierzami wirtualnymi, zalecamy z równoważenia obciążenia perspektywy rozdystrybuować tablicy innej funkcji hypervisor hostów.
* Wiele macierzy wirtualnej (jeśli jest skonfigurowana jako serwer plików lub serwer iSCSI) można wdrożyć w Namespace rozproszonego systemu plików. Aby uzyskać szczegółowy opis kroków, przejdź do [Distributed pliku System Namespace rozwiązania z Podręcznik wdrażania magazynu chmury hybrydowej](https://www.microsoft.com/download/details.aspx?id=45507). Rozproszone replikacji systemu plików nie jest obecnie zalecane do użytku z macierzy wirtualnej. 

## <a name="see-also"></a>Zobacz także
Dowiedz się, jak [administrowania rozwiązania StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) za pomocą usługi StorSimple Manager.

