---
title: Informacje o wersji usługi StorSimple 8000 Series Update 2.2 | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i rozwiązania dla usługi StorSimple 8000 Series Update 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527119"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Informacje o wersji usługi StorSimple 8000 Series Update 2.2

## <a name="overview"></a>Omówienie
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować krytyczne nierozwiązane problemy dla usługi StorSimple 8000 Series Update 2.2. Zawierają one również listę aktualizacji oprogramowania StorSimple, które są zawarte w tej wersji.

Aktualizacji Update 2.2 mogą stosowane do każdego urządzenia StorSimple z wersji (GA) lub aktualizacji 0.1 za pośrednictwem Update 2.1. Wersja urządzenia skojarzone z aktualizacją Update 2.2 jest 6.3.9600.17708.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 2.2 zawiera tylko aktualizacje oprogramowania. Trwa około 1,5-2 godzin do zainstalowania tej aktualizacji. 
> * Używasz Update 2.1, firma Microsoft zaleca stosowanie aktualizacji 2.2 tak szybko, jak to możliwe.
> * Do nowych wersji, mogą nie być widoczne aktualizacje od razu, ponieważ robimy etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-22"></a>What's new in Update 2.2
Następujące ulepszenia klucza zostały dokonane w Update 2.2.

* **Automatyczne odzyskiwanie optymalizacje** — gdy dane są usuwane na woluminy alokowane elastycznie potrzebę bloki nieużywane magazynu można odzyskać. W tej wersji ulepszono proces odzyskiwanie miejsca z chmury, co nieużywane miejsce staje się dostępna szybciej w porównaniu do poprzednich wersji.
* **Ulepszenia wydajności migawki** – 2.2 aktualizacji ulepszono czasu przetwarzania w chmurze migawki w niektórych scenariuszach, w przypadku, gdy duże woluminy są używane i jest minimalny, aby nie współczynnik zmian danych. Scenariusz, w którym będą korzystać z to ulepszenie byłoby woluminów archiwów.
* **Ograniczenie funkcjonalności pakietu dla pomocy technicznej zbierania** — wprowadziliśmy ulepszenia w taki sposób, pakiet dla pomocy technicznej są zbierane i przekazywane w tej wersji. 
* **Aktualizuj poprawki niezawodności** — ta wersja zawiera poprawki błędów, których wynikiem Zwiększona niezawodność aktualizacji.

## <a name="issues-fixed-in-update-22"></a>Problemy rozwiązane w Update 2.2
Poniższe tabele zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji 2.2 i 2.1.    

| Nie | Cecha | Problem | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Wydajność hosta |Problemy z wydajnością po stronie hosta zaobserwowano w starszej wersji, podczas tworzenia woluminu przypiętego lokalnie i w wyniku konwersji woluminu warstwowego woluminu przypiętego lokalnie. Te problemy zostały rozwiązane w tej wersji, powodując poprawę wydajności hosta podczas wykonywania procedur tworzenia i konwersji woluminu. |Yes |Nie |
| 2 |Lokalnie przypięte woluminy |W rzadkich przypadkach system ulegał awarii podczas tworzenia woluminu przypiętego lokalnie. Ten błąd został naprawiony w tej wersji. |Yes |Nie |
| 3 |Obsługa warstw |Wystąpiły sporadyczne awarie, gdy metadane dla urządzeń StorSimple w chmurze (8010 i 8020) warstwy do chmury. Ten problem jest rozwiązany w tej wersji. |Nie |Yes |
| 4 |Tworzenie migawki |Wystąpiły problemy związane z tworzenia migawek przyrostowych w scenariuszach z dużymi ilościami i minimalnym się do utraty nie danych. Te problemy zostały rozwiązane w tej wersji. |Yes |Yes |
| 5 |Openstack uwierzytelniania |Korzystając z Openstack jako dostawca usług w chmurze, użytkownik może działać w rzadkich usterki związane z uwierzytelnianiem, gdzie analizatora składni JSON spowodowały awarię. Ten problem został rozwiązany w tej wersji. |Yes |Nie |
| 6 |Kopiuj po stronie hosta |We wcześniejszych wersjach oprogramowania rzadkie usterki związane z czasu funkcji ODX występowało podczas kopiowania danych z jednego woluminu na innym woluminie. Spowodowałoby to praca awaryjna kontrolera, a system może potencjalnie przejdą w tryb odzyskiwania. Ten problem został rozwiązany w tej wersji. |Yes |Nie |
| 7 |Instrumentacja zarządzania Windows (WMI) |W poprzednich wersjach oprogramowania były kilka wystąpień awarii serwera proxy sieci web, z wyjątkiem "\<managementexception — > Błąd ładowania dostawcy". Ta usterka została przypisana do wycieku pamięci WMI i został rozwiązany. |Yes |Nie |
| 8 |Aktualizacja |W niektórych rzadkich przypadkach, w poprzednich wersjach oprogramowania "CisPowershellHcsscripterror" odebrane przez użytkownika, podczas próby skanowania lub instalowania aktualizacji. Ten problem jest rozwiązany w tej wersji. |Yes |Yes |
| 9 |Pakiet dla pomocy technicznej |W tej wersji zostały ulepszenia sposobu gromadzenia i przekazać pakietu dla pomocy technicznej. |Yes |Yes |

## <a name="known-issues-in-update-22"></a>Znane problemy w programie Update 2.2
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze / obejście | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeśli większość dysków w obudowie EBOD się do urządzenia 8600 są odłączone skutkuje dysku kworum, następnie puli magazynu przejdzie w trybie offline. Pozostanie w trybie offline, nawet wtedy, gdy dyski są zakończone. |Będzie konieczne ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą firmy Microsoft Support dla następnych kroków. |Yes |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Podczas zastępowania kontrolera kontrolera 0 może stanowić kontrolera 1. Podczas zastępowania kontrolera gdy obraz jest ładowany z węzła równorzędnego identyfikator kontrolera było wyświetlane początkowo jako identyfikator kontrolera elementów równorzędnych. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Użytkownik jest wymagana żadna akcja. Ta sytuacja sam się rozwiąże po zakończeniu zastępczego kontrolera. |Yes |Nie |
| 3 |Konta magazynu |Za pomocą usługi Storage można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. | |Yes |Yes |
| 4 |Tryb failover urządzenia |Wielu przejścia w tryb failover kontenera woluminów na tym samym urządzeniu źródła urządzeniom inny element docelowy nie jest obsługiwane. Tryb failover z jednym urządzeniu martwy do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu w trybie Failover utracić prawa własności do danych. Po włączeniu tych trybu failover te kontenery woluminów wyglądają lub działają inaczej w przypadku można je wyświetlić w klasycznym portalu Azure. | |Yes |Nie |
| 5 |Instalacja |Podczas adaptera StorSimple dla instalacji programu SharePoint musisz podać adres IP urządzenia, w kolejności do pomyślnego ukończenia instalacji. | |Yes |Nie |
| 6 |Serwer proxy sieci Web |Jeśli w konfiguracji serwera proxy sieci web HTTPS jako określony protokół, komunikacja usługi urządzeń będzie to miało wpływu i urządzenie przejdą w tryb offline. Obsługa pakietów również zostanie wygenerowany w procesie zużywają znaczne zasoby na urządzeniu z systemem. |Upewnij się, że adres URL serwera proxy sieci web ma HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Yes |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurować i włączyć serwer proxy sieci web na zarejestrowanym urządzeniu, należy uruchomić ponownie aktywny kontroler, na urządzeniu. | |Yes |Nie |
| 8 |Czas oczekiwania w chmurze o wysokiej i wysokiego obciążenia We/Wy |Gdy urządzenia StorSimple napotyka chmura bardzo duże opóźnienia (kolejność w sekundach) i wysokiego obciążenia We/Wy, do woluminów urządzenia przechodzi w stan obniżonej wydajności i operacji We/Wy może zakończyć się niepowodzeniem z powodu błędu "urządzenie nie jest gotowy". |Należy ręcznie ponownie uruchomić kontrolery urządzeń lub urządzeń w tryb failover do odzyskania z tej sytuacji. |Yes |Nie |
| 9 |Azure PowerShell |Kiedy używasz polecenia cmdlet usługi StorSimple **Get AzureStorSimpleStorageAccountCredential &#124; Select-Object - najpierw 1 - Wait** do wybrania pierwszego obiektu tak, aby utworzyć nową **elementu VolumeContainer** obiekt, polecenie cmdlet zwraca wszystkie obiekty. |Opakowanie polecenia cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - najpierw 1 - Wait** |Yes |Yes |
| 10 |Migracja |W przypadku wielu kontenerów woluminów są przekazywane do migracji, Inżyniera najnowszej kopii zapasowej jest prawidłowe tylko dla pierwszego kontenera woluminów. Ponadto migracji równoległej rozpocznie się po pierwszych 4 kopie zapasowe w pierwszym kontenera woluminów są migrowane. |Zaleca się migrację jeden kontener woluminów naraz. |Yes |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad kopii zapasowych lub grupy dysków wirtualnych. |Należy dodać te woluminy do zasad kopii zapasowych w celu tworzenia kopii zapasowych. |Yes |Yes |
| 12 |Migracja |Po zakończeniu migracji urządzeń serii 5000 i 7000 nie musi uzyskać dostęp do kontenerów zmigrowanych danych. |Zaleca się usuwanie kontenerów migrowanych danych, po migracji, ukończone i zatwierdzone. |Yes |Nie |
| 13 |Klonuj i odzyskiwanie po awarii |Urządzenia StorSimple z aktualizacją Update 1 nie można sklonować lub odzyskiwanie danych po awarii na urządzeniu z systemem przed aktualizacją oprogramowania 1. |Należy zaktualizować urządzenie docelowe do Update 1, aby zezwolić na te operacje |Yes |Yes |
| 14 |Migracja |Kopia zapasowa konfiguracji migracji może zakończyć się niepowodzeniem na urządzeniach serii 5000 – 7000 przypadku grupami woluminów za pomocą skojarzonych woluminów. |Usuń wszystkie grupy pusty wolumin z woluminów skojarzonych, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Yes |Nie |
| 15 |Polecenia cmdlet programu PowerShell systemu Azure i lokalnie przypięte woluminy |Nie można utworzyć woluminu przypiętego lokalnie za pomocą poleceń cmdlet programu Azure PowerShell. (Będą umieszczane woluminów utworzonych za pomocą programu Azure PowerShell.) |Zawsze należy używać usługi StorSimple Manager do skonfigurowania woluminów przypiętych lokalnie. |Yes |Nie |
| 16 |Miejsce dostępne dla woluminów przypiętych lokalnie |Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne do nowych woluminów nie może natychmiast zaktualizować. Usługa StorSimple Manager aktualizuje lokalne miejsce dostępne mniej więcej co godzinę. |Zaczekaj godzinę przed próbą utworzenia nowego woluminu. |Yes |Nie |
| 17 |Lokalnie przypięte woluminy |Zadanie przywracania udostępnia tymczasowe migawki kopii zapasowej w katalogu kopii zapasowej, ale tylko na czas trwania zadania przywracania. Ponadto udostępnia ona grupy dysków wirtualnych z prefiksem **tmpCollection** na **zasady tworzenia kopii zapasowych** strony, ale tylko na czas trwania zadania przywracania. |To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 18 |Lokalnie przypięte woluminy |Jeśli anulowanie zadania przywracania, a do kontrolera pracy awaryjnej natychmiast po wyświetli zadanie **przywracania** zamiast **anulowane**. Jeśli zadanie przywracania kończy się niepowodzeniem, a do kontrolera pracy awaryjnej natychmiast po wyświetli zadanie **przywracania** **anulowane** zamiast. |To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 19 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania lub jeśli przywracania kończy się niepowodzeniem, a następnie odbywa się praca awaryjna kontrolera, zadania przywracania dodatkowe pojawia się na **zadań** strony. |To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 20 |Lokalnie przypięte woluminy |Jeśli użytkownik próbuje przekonwertować wolumin warstwowy (utworzone i sklonowany za pomocą 1.2 aktualizacji lub starszym) do woluminu przypiętego lokalnie i urządzeniu zaczyna brakować miejsca lub występuje awaria chmury, clone(s) może być uszkodzony. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowany za pomocą przed aktualizacją 2.1 oprogramowania. Powinna to być rzadkie scenariusza. | | |
| 21 |Konwersja woluminu |Nie są uaktualniane rekordów Acr, dołączone do woluminu w trakcie konwersji woluminu (warstwowego na lokalnie przypięty i na odwrót). Aktualizowanie rekordów Acr może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj rekordów Acr przed konwersji woluminu i nie należy wprowadzać wszelkie dalsze aktualizacji rejestru Azure container Registry w trakcie konwersji. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Aktualizacje oprogramowania układowego i kontrolera, w Update 2.2
Ta wersja ma aktualizacji oprogramowania. Jednak aktualizowania w wersji wcześniejszej niż Update 2, należy zainstalować sterownika Storport, Spaceport, a (w niektórych przypadkach) dysku aktualizacje oprogramowania układowego na urządzeniu.

Aby uzyskać więcej informacji na temat instalowania sterownika Storport, Spaceport i aktualizacje oprogramowania układowego dysku, zobacz [zainstalować Aktualizacja 2.2](storsimple-install-update-21.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizacje urządzenia wirtualnego w Update 2.2
Ta aktualizacja nie można zastosować do urządzenia wirtualnego. Nowe urządzenia wirtualnego należy ma zostać utworzony. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować Aktualizacja 2.2](storsimple-install-update-21.md) na urządzeniu StorSimple.

