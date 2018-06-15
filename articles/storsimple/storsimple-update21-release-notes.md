---
title: Informacje o wersji StorSimple 8000 serii aktualizacji w wersji 2.2 | Dokumentacja firmy Microsoft
description: Opisuje nowe funkcje, problemy i rozwiązania dla StorSimple 8000 serii aktualizacji w wersji 2.2.
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
ms.openlocfilehash: 78be340b4a47fed88f5e8c3f5741ae7024124bd5
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057846"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Informacje o wersji StorSimple 8000 serii aktualizacji w wersji 2.2

## <a name="overview"></a>Przegląd
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować problemy otwarte krytyczne dla StorSimple 8000 serii aktualizacji w wersji 2.2. Zawierają one również listę aktualizacji oprogramowania StorSimple zawarte w tej wersji.

2.2 aktualizację można zastosować do dowolnego urządzenia StorSimple z wersji (GA) lub Update 0.1 za pośrednictwem Update 2.1. Wersja urządzenia skojarzone z 2.2 aktualizacji jest 6.3.9600.17708.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 2.2 ma tylko aktualizacje oprogramowania. Trwa około 1,5 – 2 godz. Aby zainstalować tę aktualizację. 
> * Jeśli używasz Update 2.1, zaleca się zastosowanie aktualizacji 2.2 tak szybko, jak to możliwe.
> * Dostępność nowych wersji nie może wyświetlić aktualizacje od razu, ponieważ jak etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-22"></a>Nowości w aktualizacji w wersji 2.2
Następujące ulepszenia klucza zostały wprowadzone w wersji 2.2 aktualizacji.

* **Automatyczne odzyskiwanie optymalizacje** — kiedy dane są usuwane na woluminy alokowane elastycznie potrzeby bloki nieużywane magazynu można odzyskać. Ta wersja ulepszono proces odzyskiwanie miejsca z chmury, co powoduje nieużywane miejsce staje się dostępna szybciej niż w poprzednich wersjach.
* **Migawki usprawnień wydajności** – 2.2 aktualizacji ulepszono czasu przetwarzania w chmurze migawki w niektórych scenariuszach, w przypadku, gdy duże woluminy są używane i jest minimalny do nie zmian danych. Scenariusz, w którym będzie korzystać z to rozszerzenie jest woluminów archiwum.
* **Ograniczenie funkcjonalności pakietu obsługi zbierania** — były udoskonalenia w sposób pakietu obsługi jest zebrane i przekazane w tej wersji. 
* **Ulepszenia niezawodności zaktualizować** — to wydanie zawiera poprawki, które powodują powstanie większą niezawodność aktualizacji.

## <a name="issues-fixed-in-update-22"></a>Problemy rozwiązane w wersji 2.2 aktualizacji
Poniższe tabele zawiera podsumowanie problemów, które zostały usunięte w wersji 2.2 aktualizacji i 2.1.    

| Nie | Cecha | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Wydajność hosta |Problemy z wydajnością po stronie hosta zaobserwowano w starszej wersji, podczas tworzenia woluminu przypiętego lokalnie i w wyniku konwersji wolumin warstwowy woluminu przypiętego lokalnie. W tej wersji, powodując lepszą wydajność hosta podczas wykonywania procedur tworzenia i konwersji woluminu rozwiązaniu tych problemów. |Yes |Nie |
| 2 |Woluminów przypiętych lokalnie |W rzadkich przypadkach system może ulec awarii podczas tworzenia woluminu przypiętego lokalnie. Ten problem został rozwiązany w tej wersji. |Yes |Nie |
| 3 |Obsługa poziomów |Podczas metadanych dla urządzenia StorSimple chmury (urządzenia 8010 i 8020) do warstwy do chmury było sporadyczne awarie. Tego problemu w tej wersji. |Nie |Yes |
| 4 |Tworzenie migawki |Wystąpiły problemy związane z tworzenia przyrostowych migawek w scenariuszach z dużą ilością i minimalnym do nie zmian danych. Te problemy zostały rozwiązane w tej wersji. |Yes |Yes |
| 5 |Openstack uwierzytelniania |Używając Openstack jako dostawcy usług w chmurze, użytkownik może działać do usterki rzadkim związane z uwierzytelnianiem, gdzie analizatora składni JSON spowodowała awarię. Ten problem został rozwiązany w tej wersji. |Yes |Nie |
| 6 |Kopiuj po stronie hosta |We wcześniejszych wersjach oprogramowania rzadkim błędów związanych z chronometrażu ODX wystąpił podczas kopiowania danych z jednego woluminu na inny wolumin. To spowoduje awarię kontrolera, a system potencjalnie można przejść do trybu odzyskiwania. Ten problem został rozwiązany w tej wersji. |Yes |Nie |
| 7 |Instrumentacja zarządzania Windows (WMI) |W poprzednich wersjach oprogramowania, zostały kilka wystąpień awarii serwera proxy sieci web, z wyjątkiem "<ManagementException> błąd ładowania dostawcy". Ten błąd został przypisany do usługi WMI przeciek pamięci i jest stałe. |Yes |Nie |
| 8 |Aktualizacja |W niektórych rzadkich przypadkach w poprzednich wersjach oprogramowania użytkownik otrzymał "CisPowershellHcsscripterror" podczas próby skanowania lub instalowania aktualizacji. Tego problemu w tej wersji. |Yes |Yes |
| 9 |Pakiet dla pomocy technicznej |W tej wersji zostały ulepszenia sposób pakietu obsługi jest zebrane i przekazane. |Yes |Yes |

## <a name="known-issues-in-update-22"></a>Znane problemy w wersji 2.2 aktualizacji
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarz / obejście problemu | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeśli większość dysków w obudowie EBOD urządzenia 8600 rozłączenia spowodować, że nie ma dysku kworum, następnie puli magazynu przejdzie w trybie offline. Pozostanie on w trybie offline nawet wtedy, gdy dyski są podłączeni ponownie. |Należy ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z Microsoft Support, dalsze czynności. |Yes |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Po wykonaniu zastąpić kontrolera kontrolera 0 mogą wyglądać jak kontrolera 1. Podczas wymiany kontrolera podczas ładowania obrazu z węzła równorzędnego identyfikator kontrolera można wyświetlane początkowo jako identyfikator kontrolera elementu równorzędnego. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Sytuacja ta zostanie rozwiązany samoczynnie po zakończeniu zastąpienia kontrolera. |Yes |Nie |
| 3 |Konta magazynu |Korzystanie z usługi Magazyn można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. | |Yes |Yes |
| 4 |Urządzenia trybu failover |Wiele przechodzenia kontener woluminów z tego samego urządzenia źródłowego na urządzeniach inny element docelowy nie jest obsługiwane. Tryb failover z jednym urządzeniu martwy na wielu urządzeniach spowoduje, że kontenery woluminów na pierwszym przejścia w tryb failover urządzeń utraty własności danych. Po przełączeniu te kontenery woluminów są wyświetlane lub zachowywać się inaczej, podczas wyświetlania w klasycznym portalu Azure. | |Yes |Nie |
| 5 |Instalacja |Podczas StorSimple karty dla instalacji programu SharePoint musisz podać IP urządzenia, aby instalacja zakończyło się pomyślnie. | |Yes |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracji serwera proxy sieci web ma określony protokół HTTPS, będzie mieć wpływ na komunikacji usługi urządzeń i urządzenie przejdzie w tryb offline. Obsługa pakietów również zostanie wygenerowany w procesie wykorzystywanie znaczące ilości zasobów na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci web ma HTTP jako protokół określony. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Yes |Nie |
| 7 |Serwer proxy sieci Web |Jeśli możesz skonfigurować i włączyć serwer proxy sieci web na zarejestrowanym urządzeniu, będzie konieczne ponowne uruchomienie kontrolera active na urządzeniu. | |Yes |Nie |
| 8 |Wysoka chmury opóźnienia i wysokie obciążenie We/Wy |Gdy urządzenie StorSimple napotka kombinacją chmury w bardzo duże opóźnienia (kolejność sekund) i wysokie obciążenie We/Wy, woluminy urządzenia przechodzi w stan obniżeniem i operacji We/Wy może zakończyć się niepowodzeniem z powodu błędu "urządzenie nie jest gotowe". |Konieczne będzie ręczne ponownego uruchamiania kontrolerów urządzeń lub urządzeń w tryb failover do odzyskania z tej sytuacji. |Yes |Nie |
| 9 |Azure PowerShell |Jeśli używasz polecenia cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - oczekiwania najpierw 1 -** aby zaznaczyć pierwszy obiekt, w którym można utworzyć nowy **VolumeContainer** obiekt, polecenie cmdlet zwraca wszystkie obiekty. |Opakuj polecenie cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - pierwszy 1 - oczekiwania** |Yes |Yes |
| 10 |Migracja |W przypadku wielu kontenery woluminów są przekazywane do migracji, EAT dla najnowszej kopii zapasowej jest dokładna tylko dla pierwszego kontenera woluminów. Ponadto migracji równoległej rozpocznie się po pierwszych 4 kopie zapasowe w pierwszym kontenera woluminów są migrowane. |Firma Microsoft zaleca migrować jeden kontener woluminów naraz. |Yes |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad tworzenia kopii zapasowej lub grupie dysków wirtualnych. |Należy dodać te woluminy do zasad tworzenia kopii zapasowej, aby tworzenie kopii zapasowych. |Yes |Yes |
| 12 |Migracja |Po zakończeniu migracji urządzenia z serii 5000/7000 Brak dostępu do kontenerów migrowanych danych. |Zaleca się usunąć kontenery migrowanych danych, po migracji ukończone i zatwierdzone. |Yes |Nie |
| 13 |Klonowanie i odzyskiwania po awarii |Urządzenia StorSimple z aktualizacją Update 1 nie można sklonować lub wykonać odzyskiwanie po awarii na urządzeniu z systemem przed aktualizacją oprogramowania 1. |Należy zaktualizować urządzenie do aktualizacji 1, aby umożliwić te operacje |Yes |Yes |
| 14 |Migracja |Kopia zapasowa konfiguracji migracji może zakończyć się niepowodzeniem na urządzeniu z serii 5000 7000 przypadku woluminu grup skojarzonych woluminów. |Usunąć wszystkie grupy pusty woluminu z nie skojarzone woluminy, a następnie spróbuj ponownie konfigurację kopii zapasowej. |Yes |Nie |
| 15 |Polecenia cmdlet programu PowerShell systemu Azure i woluminów przypiętych lokalnie |Nie można utworzyć woluminu przypiętego lokalnie za pomocą poleceń cmdlet programu Azure PowerShell. (Będzie warstwowa każdym woluminie tworzonych za pomocą programu Azure PowerShell.) |Aby skonfigurować woluminów przypiętych lokalnie zawsze używać usługi Menedżer StorSimple. |Yes |Nie |
| 16 |Miejsce dostępne dla woluminów przypiętych lokalnie |Jeśli usuniesz woluminu przypiętego lokalnie, miejsce dostępne dla nowych woluminów mogły nie zostać zaktualizowane od razu. Aktualizacje usługi Menedżer StorSimple lokalne miejsce dostępne mniej więcej co godzinę. |Poczekaj, aż godziny przed podjęciem próby utworzenia nowego woluminu. |Yes |Nie |
| 17 |Woluminów przypiętych lokalnie |Zadanie przywracania przedstawia tymczasowego migawki kopii zapasowej w katalogu kopii zapasowej, ale tylko na czas trwania zadania przywracania. Ponadto udostępnia ona grupy dysków wirtualnych z prefiksem **tmpCollection** na **zasady tworzenia kopii zapasowej** strony, ale tylko na czas trwania zadania przywracania. |Ten problem może wystąpić, jeśli zadanie przywracania ma przypięty tylko lokalnie woluminów lub mieszane lokalnie woluminów przypiętych i warstwowego. Jeśli zadanie przywracania zawiera tylko woluminów warstwowych, ten problem nie zostanie przeprowadzona. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 18 |Woluminów przypiętych lokalnie |Jeśli anulowanie zadania przywracania, a do kontrolera pracy awaryjnej natychmiast po wyświetli zadanie **przywracania** zamiast **anulowane**. Jeśli zadanie przywracania kończy się niepowodzeniem, a do kontrolera pracy awaryjnej natychmiast po wyświetli zadanie **przywracania** **anulowane** zamiast. |Ten problem może wystąpić, jeśli zadanie przywracania ma przypięty tylko lokalnie woluminów lub mieszane lokalnie woluminów przypiętych i warstwowego. Jeśli zadanie przywracania zawiera tylko woluminów warstwowych, ten problem nie zostanie przeprowadzona. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 19 |Woluminów przypiętych lokalnie |Anulowanie zadania przywracania, lub jeśli przywracania kończy się niepowodzeniem, a następnie do kontrolera pracy awaryjnej, zadanie przywracania dodatkowe pojawi się na **zadania** strony. |Ten problem może wystąpić, jeśli zadanie przywracania ma przypięty tylko lokalnie woluminów lub mieszane lokalnie woluminów przypiętych i warstwowego. Jeśli zadanie przywracania zawiera tylko woluminów warstwowych, ten problem nie zostanie przeprowadzona. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 20 |Woluminów przypiętych lokalnie |Jeśli użytkownik próbuje przekonwertować wolumin warstwowy (utworzone i sklonowany z 1.2 aktualizacji lub starszym) do woluminu przypiętego lokalnie i urządzeniu zaczyna brakować miejsca lub awarii chmury, clone(s) może być uszkodzony. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowany z przed aktualizacją 2.1 oprogramowania. Powinno to być rzadkim scenariusza. | | |
| 21 |Konwersja woluminu |Nie aktualizuj ACRs dołączone do woluminu w trakcie konwersji woluminu (warstwowa do przypięty lokalnie lub na odwrót). Aktualizowanie ACRs może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj ACRs przed konwersji woluminu, a nie wprowadzaj żadnych późniejszych ACR aktualizacje w trakcie konwersji. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Kontroler aktualizacje w wersji 2.2 aktualizacji
Ta wersja zawiera aktualizacji oprogramowania. Jednak aktualizowania z wersji przed Update 2, należy zainstalować sterownika Storport, Spaceport, a (w niektórych przypadkach) na dysku aktualizacje oprogramowania układowego na urządzeniu.

Aby uzyskać więcej informacji na temat instalowania sterowników, Storport, Spaceport i aktualizacje oprogramowania układowego dysku, zobacz [zainstalować aktualizację 2.2](storsimple-install-update-21.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Urządzenie wirtualne aktualizacji w wersji 2.2 aktualizacji
Ta aktualizacja nie można zastosować do urządzenia wirtualnego. Nowe urządzenia wirtualnego będzie muszą zostać utworzone. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację 2.2](storsimple-install-update-21.md) na urządzeniu StorSimple.

