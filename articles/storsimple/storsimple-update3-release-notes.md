---
title: StorSimple 8000 Series Update 3 informacje o wersji
description: W tym artykule opisano nowe funkcje, problemy i obejścia aktualizacji 3 storsimple 8000 Series.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254614"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizacja 3 informacji o wersji urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i identyfikować krytyczne otwarte problemy dla StorSimple 8000 Series Update 3. Zawierają one również listę aktualizacji oprogramowania StorSimple zawartych w tej wersji. 

Aktualizacja 3 może być stosowana do dowolnego urządzenia StorSimple z systemem Release (GA) lub Update 0.1 do update 2.2. Wersja urządzenia skojarzona z aktualizacją 3 to 6.3.9600.17759.

Zapoznaj się z informacjami zawartymi w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 3 ma oprogramowanie urządzenia, sterownik LSI i oprogramowanie układowe oraz aktualizacje Storport i Spaceport. Instalacja tej aktualizacji trwa około 1,5-2 godzin. 
> * W przypadku nowych wersji aktualizacje mogą nie być widoczne natychmiast, ponieważ wprowadzamy aktualizacje stopniowo. Poczekaj kilka dni, a następnie przeskanuj w poszukiwaniu aktualizacji ponownie, ponieważ wkrótce staną się one dostępne.
> 
> 

## <a name="whats-new-in-update-3"></a>Co nowego w aktualizacji 3
W aktualizacji 3 wprowadzono następujące ulepszenia i poprawki błędów.

* **Automatyczne zmiany rekultywacji miejsca** — uruchamianie aktualizacji 3, algorytmy odzyskiwania miejsca uruchamiane na kontrolerze rezerwowym systemu w wyniku szybszego wykonania. Aby uzyskać więcej informacji na temat portów wymaganych do pracy z rekultywacją miejsca, zapoznaj się z [wymaganiami sieci StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Ulepszenia wydajności** — aktualizacja 3 poprawiła wydajność odczytu i zapisu w chmurze.
* **Ulepszenia związane z migracją** — w tej wersji wprowadzono kilka poprawek i ulepszeń dotyczących migracji z urządzeń z serii 5000/7000 do urządzeń z serii 8000. Aby uzyskać więcej informacji na temat korzystania z funkcji migracji, przejdź do przejścia na [urządzenie z serii 5000/7000 do urządzenia z serii 8000.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) 
* **Monitorowanie powiązanych poprawek** — w tej wersji naprawiono błędy związane z wykresami monitorowania, pulpitem nawigacyjnym usługi i pulpitem nawigacyjnym urządzenia.

## <a name="issues-fixed-in-update-3"></a>Problemy rozwiązane w aktualizacji 3
Poniższe tabele zawierają podsumowanie problemów, które zostały rozwiązane w aktualizacji 3.    

| Nie | Funkcja | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Migracja danych po stronie hosta |We wcześniejszej wersji urządzenie StorSimple Cloud Appliance przechodziło w tryb offline podczas migracji danych po stronie hosta. Ten problem został rozwiązany w tej wersji. |Nie |Tak |
| 2 |Lokalnie przypięte woluminy |W poprzedniej wersji wystąpiły problemy związane z błędami we/wy, błędami konwersji woluminów i błędami ścieżki danych dla woluminów przypiętych lokalnie. Te problemy zostały spowodowane przez roota i naprawione w tej wersji. |Tak |Nie |
| 3 |Monitorowanie |Wystąpiło wiele problemów związanych z jednostkami raportowania i monitorowaniem, a także wykresami pulpitu nawigacyjnego urządzenia, na których wyświetlane były nieprawidłowe informacje dotyczące woluminów przypiętych lokalnie. Te problemy zostały rozwiązane w tej wersji. |Tak |Nie |
| 4 |Ciężkie zapisy We/Wy |Podczas korzystania StorSimple dla obciążeń obejmujących ciężkich zapisów, użytkownik będzie działać na rzadki błąd, w którym zestaw roboczy był warstwowy w chmurze. Ten błąd został naprawiony w tej wersji. |Tak |Tak |
| 5 |Tworzenie kopii zapasowych |W niektórych rzadkich przypadkach w poprzednich wersjach oprogramowania, gdy użytkownik wykonał kopię zapasową zdalnego klonu, napotkali błędy w chmurze, a operacja zostałaby wyniszczana. W tej wersji problem został rozwiązany, a operacja zakończy się pomyślnie. |Tak |Tak |
| 6 |Zasady tworzenia kopii zapasowych |W niektórych rzadkich przypadkach we wcześniejszych wersjach oprogramowania wystąpił błąd związany z usunięciem zasad tworzenia kopii zapasowych. Ten problem został rozwiązany w tej wersji. |Tak |Tak |

## <a name="known-issues-in-update-3"></a>Znane problemy w aktualizacji 3
Poniższa tabela zawiera podsumowanie znanych problemów w tej wersji.

| Nie. | Funkcja | Problem | Komentarze / obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowy EBOD urządzenia 8600 są odłączone, co powoduje brak kworum dysku, a następnie puli magazynu przejdzie do trybu offline. Pozostanie w trybie offline, nawet jeśli dyski zostaną ponownie podłączone. |Musisz ponownie uruchomić urządzenie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |Tak |Nie |
| 2 |Nieprawidłowy identyfikator kontrolera |Po wykonaniu wymiany kontrolera kontroler 0 może pojawić się jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może być początkowo pokazywał się jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach to zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Ta sytuacja rozwiąże się po zakończeniu wymiany kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Za pomocą usługi magazynowania, aby usunąć konto magazynu jest nieobsługiwał scenariusz. Doprowadzi to do sytuacji, w której nie można pobrać danych użytkownika. | |Tak |Tak |
| 4 |Przewijowanie awaryjne urządzenia |Wiele przechyłów awaryjnych kontenera woluminu z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwany. Przewijanie awaryjne z jednego martwego urządzenia do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu awaryjnym utracić własność danych. Po takiej pracy awaryjnej te kontenery woluminów będą wyświetlane lub zachowywać się inaczej podczas wyświetlania ich w klasycznej witrynie Azure portal. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji karty StorSimple dla programu SharePoint należy podać adres IP urządzenia, aby instalacja zakończyła się pomyślnie. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma https jako określony protokół, komunikacja między urządzeniami zostanie naruszona, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej będą również generowane w procesie, zużywając znaczne zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma http jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-8000-configure-web-proxy.md). |Tak |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurujesz i włączysz serwer proxy sieci Web na zarejestrowanym urządzeniu, musisz ponownie uruchomić aktywny kontroler na urządzeniu. | |Tak |Nie |
| 8 |Duże opóźnienia w chmurze i duże obciążenie we/wy |Gdy urządzenie StorSimple napotka kombinację bardzo dużych opóźnień w chmurze (kolejność sekund) i wysokiego obciążenia we/wy, woluminy urządzenia przechodzą w stan pogorszenia, a we/wy mogą zakończyć się niepowodzeniem z błędem "urządzenie nie jest gotowe". |Należy ręcznie ponownie uruchomić kontrolery urządzeń lub wykonać pracy awaryjnej urządzenia, aby odzyskać z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |Korzystając z polecenia cmdlet StorSimple **Get-AzureStorSimpleStorageAccredential &#124; Select-Object -First 1 -Wait,** aby zaznaczyć pierwszy obiekt, aby można było utworzyć nowy obiekt **VolumeContainer,** polecenie cmdlet zwraca wszystkie obiekty. |Zawijanie polecenia cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Tak |Tak |
| 10 |Migracja |Gdy wiele kontenerów woluminów są przekazywane do migracji, ETA dla najnowszej kopii zapasowej jest dokładne tylko dla pierwszego kontenera woluminu. Ponadto migracja równoległa rozpocznie się po migrowanie pierwszych 4 kopii zapasowych w pierwszym kontenerze woluminu. |Zaleca się migrację jednego kontenera woluminów naraz. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad tworzenia kopii zapasowych ani do grupy dysków wirtualnych. |Aby utworzyć kopie zapasowe, należy dodać te woluminy do zasad tworzenia kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzenie serii 5000/7000 nie może uzyskać dostępu do zmigrowanych kontenerów danych. |Zaleca się usunięcie zmigrowanych kontenerów danych po zakończeniu migracji i zaajsnaniu. |Tak |Nie |
| 13 |Klon i dr |Urządzenie StorSimple z aktualizacją 1 nie może sklonować ani wykonywać odzyskiwania po awarii na urządzeniu z uruchomionym oprogramowaniem przed aktualizacją 1. |Należy zaktualizować urządzenie docelowe do aktualizacji 1, aby umożliwić te operacje |Tak |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji dla migracji może zakończyć się niepowodzeniem na urządzeniu z serii 5000-7000, gdy istnieją grupy woluminów bez skojarzonych woluminów. |Usuń wszystkie puste grupy woluminów bez skojarzonych woluminów, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Tak |Nie |
| 15 |Polecenia cmdlet programu Azure PowerShell i woluminy przypięte lokalnie |Nie można utworzyć woluminu przypiętego lokalnie za pośrednictwem poleceń cmdlet programu Azure PowerShell. (Każdy wolumin utworzony za pośrednictwem programu Azure PowerShell będzie warstwowy).) |Zawsze używaj usługi StorSimple Manager do konfigurowania woluminów przypiętych lokalnie. |Tak |Nie |
| 16 |Dostępne miejsce na woluminy przypięte lokalnie |Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa StorSimple Manager aktualizuje lokalne miejsce dostępne około co godzinę. |Poczekaj godzinę, zanim spróbujesz utworzyć nowy wolumin. |Tak |Nie |
| 17 |Lokalnie przypięte woluminy |Zadanie przywracania udostępnia tymczasową kopię zapasową migawki w wykazie kopii zapasowych, ale tylko na czas trwania zadania przywracania. Ponadto udostępnia grupę dysków wirtualnych z prefiksem **tmpCollection** na stronie **Zasady tworzenia kopii zapasowych,** ale tylko na czas trwania zadania przywracania. |To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 18 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania, a kontroler przejdzie w stan failover natychmiast po tym, zadanie przywracania będzie wyświetlane **nie powiodło się** zamiast **Anulowane**. Jeśli zadanie przywracania nie powiedzie się, a kontroler przejdzie w stan failover natychmiast po, zadanie przywracania wyświetli **anulowane** zamiast **nie powiodło się**. |To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 19 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania lub jeśli przywracanie nie powiedzie się, a następnie nastąpi praca awaryjna kontrolera, na stronie **Zadania** zostaną wyświetlona dodatkowe zadanie przywracania. |To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 20 |Lokalnie przypięte woluminy |Jeśli spróbujesz przekonwertować wolumin warstwowy (utworzony i sklonowany za pomocą aktualizacji 1.2 lub wcześniejszej) na wolumin przypięty lokalnie, a na urządzeniu zaczyna brakować miejsca lub występuje awaria chmury, klony mogą zostać uszkodzone. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowane za pomocą oprogramowania przed aktualizacją 2.1. Powinno to być rzadkie scenariusz. | | |
| 21 |Konwersja woluminów |Nie należy aktualizować AKS dołączonych do woluminu, gdy trwa konwersja woluminu (warstwowa do przypiętych lokalnie lub odwrotnie). Aktualizacja AKS może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj ACR przed konwersją woluminu i nie wprowadzaj żadnych dalszych aktualizacji ACR w trakcie konwersji. | | |
| 22 |Aktualizacje |Podczas stosowania aktualizacji 3, na stronie **konserwacji** w klasycznej witrynie Azure portal wyświetli następujący komunikat związany z aktualizacją 2 — "StorSimple 8000 series Update 2 zawiera możliwość firmy Microsoft proaktywnie zbierać informacje dziennika z urządzenia, gdy wykryjemy potencjalne problemy". Jest to mylące, ponieważ wskazuje, że urządzenie jest aktualizowane do aktualizacji 2. Po pomyślnym zaktualizowaniu urządzenia do aktualizacji 3 ten komunikat zniknie. |To zachowanie zostanie naprawione w przyszłej wersji. |Tak |Nie |

## <a name="controller-and-firmware-updates-in-update-3"></a>Aktualizacje kontrolera i oprogramowania układowego w aktualizacji 3
W tej wersji dostępne są aktualizacje sterowników LSI i oprogramowania układowego. Aby uzyskać więcej informacji na temat instalowania aktualizacji sterownika LSI i oprogramowania układowego, zobacz [instalowanie aktualizacji 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 3
Tej aktualizacji nie można zastosować do urządzenia StorSimple Cloud Appliance (znanego również jako urządzenie wirtualne). Konieczne będzie utworzenie nowych urządzeń wirtualnych. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

