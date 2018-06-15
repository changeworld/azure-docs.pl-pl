---
title: Informacje o wersji StorSimple 8000 serii aktualizacji 3 | Dokumentacja firmy Microsoft
description: Opisuje nowe funkcje, problemy i rozwiązania dla StorSimple 8000 serii Update 3.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9308602b77903e553e26d6f0586d963a4c58230d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057829"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizacja 3 informacje o wersji dla danego urządzenia z serii StorSimple 8000

## <a name="overview"></a>Przegląd
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować problemy otwarte krytyczne dla StorSimple 8000 serii Update 3. Zawierają one również listę aktualizacji oprogramowania StorSimple zawarte w tej wersji. 

Aktualizacja 3 może odnosić się do każdego urządzenia StorSimple z wersji (GA) lub Update 0.1 za pomocą aktualizacji w wersji 2.2. Wersja urządzenia skojarzone z aktualizacją Update 3 jest 6.3.9600.17759.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 3 ma oprogramowanie urządzenia, LSI sterowników i oprogramowania układowego i Storport i Spaceport aktualizacji. Trwa około 1,5 – 2 godz. Aby zainstalować tę aktualizację. 
> * Dostępność nowych wersji nie może wyświetlić aktualizacje od razu, ponieważ jak etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-3"></a>Nowości w wersji Update 3
W wersji Update 3 wprowadzono następujące ulepszenia klucza i poprawki.

* **Automatyczne zmiany odzyskiwanie miejsca** — uruchamianie Update 3 algorytmów odzyskiwanie miejsca jest uruchamiane na kontrolerze rezerwy systemu, co powoduje szybsze wykonywanie. Aby uzyskać więcej informacji na portach, które są wymagane do wykonania odzyskiwania miejsca, zapoznaj się [StorSimple wymagań sieciowych](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Ulepszenia wydajności** — Update 3 ma wyższą wydajność odczytu i zapisu do chmury.
* **Ulepszenia związane z migracją** — w tej wersji kilka poprawek i ulepszenia zostały przygotowane dla funkcji migracji z urządzeń z serii 5000/7000 do urządzeń z serii 8000. Aby uzyskać więcej informacji na temat korzystania z funkcji migracji, przejdź do [migracja z serii 5000/7000 urządzeń na urządzenie 8000 series](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorowanie pokrewne poprawki** — w tej wersji błędy związane z monitorowania wykresy, odwiedź pulpit nawigacyjny usługi i urządzenia pulpitu nawigacyjnego zostały usunięte.

## <a name="issues-fixed-in-update-3"></a>Problemy rozwiązane w ramach aktualizacji Update 3
Poniższe tabele zawiera podsumowanie problemów, które zostały usunięte w wersji Update 3.    

| Nie | Cecha | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Migracja danych po stronie hosta |W starszych wersji urządzenia StorSimple w chmurze została przechodzi do trybu offline podczas migracji danych po stronie hosta. Tego problemu w tej wersji. |Nie |Yes |
| 2 |Woluminów przypiętych lokalnie |W poprzednich wersjach wystąpiły problemy związane z błędów We/Wy, niepowodzenia konwersji woluminu i awarie ścieżki danych dla woluminów przypiętych lokalnie. Te problemy zostały spowodowane głównego i stałym w tej wersji. |Yes |Nie |
| 3 |Monitorowanie |Znaleziono wiele problemów związanych z raportowania jednostki i monitorowania, a także wykresy pulpitu nawigacyjnego urządzenia, którym nieprawdziwych informacji został wyświetlony dla woluminów przypiętych lokalnie. Te problemy zostały rozwiązane w tej wersji. |Yes |Nie |
| 4 |Duże zapisy we/wy |W przypadku używania StorSimple dla obciążeń obejmujących duże zapisy, użytkownik może działać w rzadkim usterki gdzie zestaw roboczy został trwa warstwy do chmury. Ten problem został rozwiązany w tej wersji. |Yes |Yes |
| 5 |Backup |W niektórych rzadkich przypadkach w poprzednich wersjach oprogramowania gdy użytkownik przejął kopii zapasowej zdalnego klonowania, czy wystąpiły błędy chmury i operacji spowoduje błąd. W tej wersji problem został rozwiązany i operacja zakończy się pomyślnie. |Yes |Yes |
| 6 |Zasady tworzenia kopii zapasowych |W niektórych rzadkich przypadkach we wcześniejszych wersjach oprogramowania, wystąpił błąd dotyczące usuwania zasad tworzenia kopii zapasowej. Tego problemu w tej wersji. |Yes |Yes |

## <a name="known-issues-in-update-3"></a>Znane problemy w wersji Update 3
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarz / obejście problemu | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeśli większość dysków w obudowie EBOD urządzenia 8600 rozłączenia spowodować, że nie ma dysku kworum, następnie puli magazynu przejdzie w trybie offline. Pozostanie on w trybie offline nawet wtedy, gdy dyski są podłączeni ponownie. |Należy ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z Microsoft Support, dalsze czynności. |Yes |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Po wykonaniu zastąpić kontrolera kontrolera 0 mogą wyglądać jak kontrolera 1. Podczas wymiany kontrolera podczas ładowania obrazu z węzła równorzędnego identyfikator kontrolera można wyświetlane początkowo jako identyfikator kontrolera elementu równorzędnego. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Sytuacja ta zostanie rozwiązany samoczynnie po zakończeniu zastąpienia kontrolera. |Yes |Nie |
| 3 |Konta magazynu |Korzystanie z usługi Magazyn można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. | |Yes |Yes |
| 4 |Urządzenia trybu failover |Wiele przechodzenia kontener woluminów z tego samego urządzenia źródłowego na urządzeniach inny element docelowy nie jest obsługiwane. Tryb failover z jednym urządzeniu martwy na wielu urządzeniach spowoduje, że kontenery woluminów na pierwszym przejścia w tryb failover urządzeń utraty własności danych. Po przełączeniu te kontenery woluminów są wyświetlane lub zachowywać się inaczej, podczas wyświetlania w klasycznym portalu Azure. | |Yes |Nie |
| 5 |Instalacja |Podczas StorSimple karty dla instalacji programu SharePoint musisz podać IP urządzenia, aby instalacja zakończyło się pomyślnie. | |Yes |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracji serwera proxy sieci web ma określony protokół HTTPS, będzie mieć wpływ na komunikacji usługi urządzeń i urządzenie przejdzie w tryb offline. Obsługa pakietów również zostanie wygenerowany w procesie wykorzystywanie znaczące ilości zasobów na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci web ma HTTP jako protokół określony. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-8000-configure-web-proxy.md). |Yes |Nie |
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
| 22 |Aktualizacje |Podczas stosowania aktualizacji 3 **konserwacji** strony w klasycznym portalu Azure wyświetli następujący komunikat związane z Update 2 — "serii StorSimple 8000 z aktualizacją Update 2 zawiera możliwości dla firmy Microsoft do aktywnego zbieranie dzienników informacje z urządzenia, gdy zostanie wykryte, potencjalne problemy." To jest błąd, ponieważ wskazuje, że urządzenie jest aktualizowana do wersji Update 2. Gdy urządzenie jest succeesfully aktualizacji do wersji Update 3, ten komunikat zniknie. |Ten problem zostanie rozwiązany w przyszłej wersji. |Yes |Nie |

## <a name="controller-and-firmware-updates-in-update-3"></a>Kontroler aktualizacje w wersji Update 3
Ta wersja zawiera LSI sterowników i oprogramowania układowego. Aby uzyskać więcej informacji na temat instalowania aktualizacji oprogramowania układowego i sterownika LSI, zobacz [zainstalować aktualizacji 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Urządzenie wirtualne aktualizacji w wersji Update 3
Ta aktualizacja nie można zastosować do urządzenia chmury StorSimple (znanej także jako urządzenia wirtualnego). Nowe urządzenia wirtualnego będzie muszą zostać utworzone. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizacji 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

