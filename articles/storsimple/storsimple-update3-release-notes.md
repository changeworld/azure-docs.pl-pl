---
title: StorSimple 8000 Series Update 3 — informacje o wersji | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i rozwiązania dla usługi StorSimple 8000 Series Update 3.
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
ms.openlocfilehash: d18feba4ded3dfccb8f774112a7dc8d42b12f1d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530954"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Update 3 informacje o wersji dla urządzenia StorSimple 8000 series

## <a name="overview"></a>Omówienie
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować krytyczne nierozwiązane problemy dla usługi StorSimple 8000 Series Update 3. Zawierają one również listę aktualizacji oprogramowania StorSimple, które są zawarte w tej wersji. 

Update 3 można zastosować na dowolne urządzenie StorSimple z wersji (GA) lub aktualizacji 0.1 za pośrednictwem aktualizacji 2.2. Wersja urządzenia skojarzone z aktualizacją Update 3 jest 6.3.9600.17759.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Update 3 zawiera oprogramowanie urządzenia, LSI sterowników i oprogramowania układowego i Storport i Spaceport aktualizacji. Trwa około 1,5-2 godzin do zainstalowania tej aktualizacji. 
> * Do nowych wersji, mogą nie być widoczne aktualizacje od razu, ponieważ robimy etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-3"></a>Co nowego w wersji Update 3
W wersji Update 3 wprowadzono następujące ulepszenia klucza i poprawek błędów oprogramowania.

* **Automatyczne zmiany odzyskiwanie miejsca** — uruchamianie, aktualizacja 3, algorytmy odzyskiwanie miejsca uruchomić na kontroler zapasowy systemu, co spowoduje szybsze wykonywanie. Aby uzyskać więcej informacji na temat portów, które są wymagane do pracy z odzyskiwanie miejsca, zobacz [StorSimple wymagania sieciowe](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Ulepszenia wydajności** — Update 3 została zwiększona wydajność odczytu i zapisu do chmury.
* **Ulepszenia związane z migracją** — w tej wersji wprowadzono kilka poprawek i ulepszenia zostały wykonane dla funkcji migracji z urządzeń serii 5000 i 7000 urządzeń z serii 8000. Aby uzyskać więcej informacji na temat korzystania z funkcji migracji, przejdź do [migracji z urządzeń serii 5000 i 7000 do urządzenia z serii 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorowanie powiązane poprawki** — w tej wersji błędy powiązane z monitorowaniem wykresów, pulpitu nawigacyjnego usługi, i zostały rozwiązane w pulpicie nawigacyjnym urządzenia.

## <a name="issues-fixed-in-update-3"></a>Problemy rozwiązane w wersji Update 3
Poniższe tabele zawiera podsumowanie problemów, które zostały rozwiązane w wersji Update 3.    

| Nie | Cecha | Problem | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Migracja danych po stronie hosta |W poprzedniej wersji urządzenia StorSimple w chmurze zostało przechodzi do trybu offline podczas migracji danych po stronie hosta. Ten problem jest rozwiązany w tej wersji. |Nie |Yes |
| 2 |Lokalnie przypięte woluminy |W poprzedniej wersji wystąpiły problemy związane z operacji We/Wy błędy niepowodzenia konwersji woluminu i awarie ścieżki danych dla woluminów przypiętych lokalnie. Te problemy zostały spowodowane głównego i rozwiązane w tej wersji. |Yes |Nie |
| 3 |Monitorowanie |Wystąpiło wiele problemów związanych z raportowania jednostek i monitorowania oraz wykresy pulpitu nawigacyjnego urządzeń, w którym błędne informacje były wyświetlane dla woluminów przypiętych lokalnie. Te problemy zostały rozwiązane w tej wersji. |Yes |Nie |
| 4 |Duże zapisy we/wy |Korzystając z usługi StorSimple dla obciążeń obejmujących duże zapisy, użytkownik może działać w sporadycznych błędów, gdzie zestaw roboczy został on warstwy do chmury. Ten problem został rozwiązany w tej wersji. |Yes |Yes |
| 5 |Backup |W niektórych rzadkich przypadkach, w poprzednich wersjach oprogramowania po użytkownik przejął kopii zapasowej klonowania zdalnego, działają w chmurze błędy, a operacja spowoduje błąd limitu. W tej wersji problem zostanie rozwiązany, a operacja zakończy się pomyślnie. |Yes |Yes |
| 6 |Zasady tworzenia kopii zapasowych |W niektórych przypadkach rzadkich we wcześniejszych wersjach oprogramowania, było usterki związane z usuwanie zasad kopii zapasowych. Ten problem jest rozwiązany w tej wersji. |Yes |Yes |

## <a name="known-issues-in-update-3"></a>Znane problemy w wersji Update 3
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze / obejście | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeśli większość dysków w obudowie EBOD się do urządzenia 8600 są odłączone skutkuje dysku kworum, następnie puli magazynu przejdzie w trybie offline. Pozostanie w trybie offline, nawet wtedy, gdy dyski są zakończone. |Będzie konieczne ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą firmy Microsoft Support dla następnych kroków. |Yes |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Podczas zastępowania kontrolera kontrolera 0 może stanowić kontrolera 1. Podczas zastępowania kontrolera gdy obraz jest ładowany z węzła równorzędnego identyfikator kontrolera było wyświetlane początkowo jako identyfikator kontrolera elementów równorzędnych. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Użytkownik jest wymagana żadna akcja. Ta sytuacja sam się rozwiąże po zakończeniu zastępczego kontrolera. |Yes |Nie |
| 3 |Konta magazynu |Za pomocą usługi Storage można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. | |Yes |Yes |
| 4 |Tryb failover urządzenia |Wielu przejścia w tryb failover kontenera woluminów na tym samym urządzeniu źródła urządzeniom inny element docelowy nie jest obsługiwane. Tryb failover z jednym urządzeniu martwy do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu w trybie Failover utracić prawa własności do danych. Po włączeniu tych trybu failover te kontenery woluminów wyglądają lub działają inaczej w przypadku można je wyświetlić w klasycznym portalu Azure. | |Yes |Nie |
| 5 |Instalacja |Podczas adaptera StorSimple dla instalacji programu SharePoint musisz podać adres IP urządzenia, w kolejności do pomyślnego ukończenia instalacji. | |Yes |Nie |
| 6 |Serwer proxy sieci Web |Jeśli w konfiguracji serwera proxy sieci web HTTPS jako określony protokół, komunikacja usługi urządzeń będzie to miało wpływu i urządzenie przejdą w tryb offline. Obsługa pakietów również zostanie wygenerowany w procesie zużywają znaczne zasoby na urządzeniu z systemem. |Upewnij się, że adres URL serwera proxy sieci web ma HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-8000-configure-web-proxy.md). |Yes |Nie |
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
| 22 |Aktualizacje |Podczas stosowania aktualizacji 3 **konserwacji** strony w klasycznym portalu Azure wyświetli następujący komunikat związane z aktualizacją Update 2 — "StorSimple 8000 series Update 2 obejmuje możliwości firmy Microsoft do proaktywne zbieranie dzienników informacje o z urządzenia po wykryciu potencjalnych problemów". To jest mylące, ponieważ oznacza to, że urządzenie jest aktualizowany do Update 2. Po pomyślnym zaktualizowaniu urządzenia do wersji Update 3, wiadomość zniknie. |Ten problem zostanie rozwiązany w przyszłej wersji. |Yes |Nie |

## <a name="controller-and-firmware-updates-in-update-3"></a>Aktualizacje oprogramowania układowego i kontrolera, w wersji Update 3
Ta wersja ma LSI aktualizacje oprogramowania układowego i sterowników. Aby uzyskać więcej informacji na temat instalowania aktualizacji oprogramowania układowego i sterownika LSI, zobacz [instalowanie aktualizacji 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizacje urządzenia wirtualnego w wersji Update 3
Ta aktualizacja nie można zastosować do urządzenia StorSimple w chmurze (znany także jako urządzenie wirtualne). Nowe urządzenia wirtualnego należy ma zostać utworzony. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [instalowanie aktualizacji 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

