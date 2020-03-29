---
title: StorSimple 8000 Series Update 2.2 informacje o wersji | Dokumenty firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i obejścia aktualizacji 2.2 storsimple z serii 8000.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531053"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series Update 2.2 informacje o wersji

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowano krytyczne otwarte problemy dla aktualizacji 2.2 storsimple z serii 8000. Zawierają one również listę aktualizacji oprogramowania StorSimple zawartych w tej wersji.

Aktualizacja 2.2 może być stosowana do dowolnego urządzenia StorSimple z systemem Release (GA) lub Update 0.1 do aktualizacji 2.1. Wersja urządzenia skojarzona z aktualizacją 2.2 to 6.3.9600.17708.

Zapoznaj się z informacjami zawartymi w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 2.2 ma tylko aktualizacje oprogramowania. Instalacja tej aktualizacji trwa około 1,5-2 godzin. 
> * Jeśli korzystasz z aktualizacji 2.1, zalecamy jak najszybsze zastosowanie aktualizacji 2.2.
> * W przypadku nowych wersji aktualizacje mogą nie być widoczne natychmiast, ponieważ wprowadzamy aktualizacje stopniowo. Poczekaj kilka dni, a następnie przeskanuj w poszukiwaniu aktualizacji ponownie, ponieważ wkrótce staną się one dostępne.
> 
> 

## <a name="whats-new-in-update-22"></a>Co nowego w aktualizacji 2.2
W aktualizacji 2.2 wprowadzono następujące kluczowe ulepszenia.

* **Automatyczna optymalizacja odzyskiwania miejsca** — gdy dane są usuwane na woluminach nieobe aprowizacji, nieużywane bloki magazynu muszą zostać odzyskane. Ta wersja usprawniła proces rekultywacji miejsca z chmury, co spowodowało, że nieużywane miejsce stało się dostępne szybciej w porównaniu z poprzednimi wersjami.
* **Ulepszenia wydajności migawki** — aktualizacja 2.2 poprawiła czas przetwarzania migawki chmury w niektórych scenariuszach, w których używane są duże woluminy i nie ma minimalnego do żadnego wpływu na dane. Scenariusz, który skorzystałby z tego ulepszenia byłoby woluminów archiwum.
* **Wzmocnienie gromadzenia pakietów pomocy technicznej** — wprowadzono ulepszenia w sposobie zbierania i przesyłania pakietu pomocy technicznej w tej wersji. 
* **Ulepszenia niezawodności aktualizacji** — w tej wersji wprowadzono poprawki błędów, które powodują poprawę niezawodności aktualizacji.

## <a name="issues-fixed-in-update-22"></a>Problemy rozwiązane w aktualizacji 2.2
Poniższe tabele zawierają podsumowanie problemów, które zostały rozwiązane w aktualizacjach 2.2 i 2.1.    

| Nie | Funkcja | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Wydajność hosta |We wcześniejszej wersji problemy z wydajnością po stronie hosta zostały zaobserwowane podczas tworzenia woluminu przypiętego lokalnie i podczas konwersji woluminu warstwowego na wolumin przypięty lokalnie. Te problemy zostały rozwiązane w tej wersji, co powoduje poprawę wydajności hosta podczas tworzenia woluminu i procedur konwersji. |Tak |Nie |
| 2 |Lokalnie przypięte woluminy |W rzadkich przypadkach system ulega awarii podczas tworzenia woluminu przypiętego lokalnie. Ten błąd został naprawiony w tej wersji. |Tak |Nie |
| 3 |Poziomy |Zdarzały się sporadyczne awarie, gdy metadane urządzeń w chmurze StorSimple (8010 i 8020) warstwowe do chmury. Ten problem został rozwiązany w tej wersji. |Nie |Tak |
| 4 |Tworzenie migawki |Wystąpiły problemy związane z tworzeniem migawek przyrostowych w scenariuszach z dużymi woluminami i minimalnym lub żadnym zmianem danych. Te problemy zostały rozwiązane w tej wersji. |Tak |Tak |
| 5 |Uwierzytelnianie openstack |Podczas korzystania z Openstack jako dostawcy usług w chmurze, użytkownik będzie działać na rzadki błąd związany z uwierzytelnianiem, gdzie parser JSON spowodowało awarię. Ten błąd został naprawiony w tej wersji. |Tak |Nie |
| 6 |Kopia po stronie hosta |We wcześniejszych wersjach oprogramowania podczas kopiowania danych z jednego woluminu do innego zaobserwowano rzadki błąd związany z taktowaniem ODX. Spowodowałoby to, że kontroler przechodzi w tryb failover, a system może potencjalnie przejść w tryb odzyskiwania. Ten błąd został naprawiony w tej wersji. |Tak |Nie |
| 7 |Instrumentacja zarządzania windowsem (WMI) |W poprzednich wersjach oprogramowania było kilka wystąpień awarii serwera\<proxy sieci web z wyjątkiem "ManagementException> Provider load failure". Ten błąd został przypisany do przecieku pamięci WMI i został naprawiony. |Tak |Nie |
| 8 |Aktualizacja |W niektórych rzadkich przypadkach, w poprzednich wersjach oprogramowania, użytkownik otrzymał "CisPowershellHcsscripterror" podczas próby skanowania lub instalowania aktualizacji. Ten problem został rozwiązany w tej wersji. |Tak |Tak |
| 9 |Pakiet pomocy technicznej |W tej wersji wprowadzono ulepszenia dotyczące sposobu, w jaki pakiet pomocy technicznej jest zbierany i przesyłany. |Tak |Tak |

## <a name="known-issues-in-update-22"></a>Znane problemy w aktualizacji 2.2
Poniższa tabela zawiera podsumowanie znanych problemów w tej wersji.

| Nie. | Funkcja | Problem | Komentarze / obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowy EBOD urządzenia 8600 są odłączone, co powoduje brak kworum dysku, a następnie puli magazynu przejdzie do trybu offline. Pozostanie w trybie offline, nawet jeśli dyski zostaną ponownie podłączone. |Musisz ponownie uruchomić urządzenie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |Tak |Nie |
| 2 |Nieprawidłowy identyfikator kontrolera |Po wykonaniu wymiany kontrolera kontroler 0 może pojawić się jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może być początkowo pokazywał się jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach to zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Ta sytuacja rozwiąże się po zakończeniu wymiany kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Za pomocą usługi magazynowania, aby usunąć konto magazynu jest nieobsługiwał scenariusz. Doprowadzi to do sytuacji, w której nie można pobrać danych użytkownika. | |Tak |Tak |
| 4 |Przewijowanie awaryjne urządzenia |Wiele przechyłów awaryjnych kontenera woluminu z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwany. Przewijanie awaryjne z jednego martwego urządzenia do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu awaryjnym utracić własność danych. Po takiej pracy awaryjnej te kontenery woluminów będą wyświetlane lub zachowywać się inaczej podczas wyświetlania ich w klasycznej witrynie Azure portal. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji karty StorSimple dla programu SharePoint należy podać adres IP urządzenia, aby instalacja zakończyła się pomyślnie. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma https jako określony protokół, komunikacja między urządzeniami zostanie naruszona, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej będą również generowane w procesie, zużywając znaczne zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma http jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Tak |Nie |
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

## <a name="controller-and-firmware-updates-in-update-22"></a>Aktualizacje kontrolera i oprogramowania układowego w aktualizacji 2.2
Ta wersja ma aktualizacje tylko do oprogramowania. Jeśli jednak aktualizujesz wersję przed aktualizacją 2, musisz zainstalować na urządzeniu aktualizacje oprogramowania sprzętowego sterownika Storport, Spaceport i (w niektórych przypadkach) oprogramowania układowego dysku.

Aby uzyskać więcej informacji na temat instalowania aktualizacji sterownika, storportu, portu kosmicznego i oprogramowania układowego dysku, zobacz [instalowanie aktualizacji 2.2](storsimple-install-update-21.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 2.2
Tej aktualizacji nie można zastosować do urządzenia wirtualnego. Konieczne będzie utworzenie nowych urządzeń wirtualnych. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację 2.2](storsimple-install-update-21.md) na urządzeniu StorSimple.

