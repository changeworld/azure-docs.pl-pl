---
title: StorSimple 8000 Series Update 1.2 informacje o wersji | Dokumenty firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i obejścia aktualizacji 1.2 storsimple z serii 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531021"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizacja 1.2 informacje o wersji urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowano krytyczne otwarte problemy dla aktualizacji 1.2 storsimple z serii 8000. Zawierają one również listę aktualizacji oprogramowania StorSimple, sterownika i oprogramowania układowego dysku zawartych w tej wersji. 

Aktualizacja 1.2 może być stosowana do dowolnego urządzenia StorSimple z systemem Release (GA), Update 0.1, Update 0.2 lub Update 0.3. Aktualizacja 1.2 nie jest dostępna, jeśli na urządzeniu jest uruchomiona aktualizacja 1 lub aktualizacja 1.1. Jeśli na urządzeniu jest uruchomiona wersja (GA), [skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-contact-microsoft-support.md) aby pomóc w zainstalowaniu tej aktualizacji.

W poniższej tabeli wymieniono wersje oprogramowania urządzenia odpowiadające aktualizacjom 1, 1.1 i 1.2.

| Jeśli uruchomisz aktualizację ... | jest to wersja oprogramowania urządzenia. |
| --- | --- |
| Aktualizacja 1.2 |6.3.9600.17584 |
| Aktualizacja 1.1 |6.3.9600.17521 |
| Aktualizacja 1.0 |6.3.9600.17491 |

Zapoznaj się z informacjami zawartymi w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple. Aby uzyskać więcej informacji, zobacz jak [zainstalować aktualizację 1.2 na urządzeniu StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Instalacja tej aktualizacji trwa około 5–10 godzin (w tym aktualizacji systemu Windows). 
> * Aktualizacja 1.2 ma oprogramowanie, sterownik LSI i aktualizacje oprogramowania układowego dysku. Aby zainstalować, postępuj zgodnie z instrukcjami [dotyczącymi instalowania aktualizacji 1.2 na urządzeniu StorSimple](storsimple-install-update-1.md).
> * W przypadku nowych wersji aktualizacje mogą nie być widoczne natychmiast, ponieważ wprowadzamy aktualizacje stopniowo. Skanuj aktualizacje w ciągu kilku dni ponownie, ponieważ wkrótce staną się one dostępne.
> 
> 

## <a name="whats-new-in-update-12"></a>Co nowego w aktualizacji 1.2
Te funkcje zostały po raz pierwszy wydane z aktualizacją 1, która została udostępniona ograniczonej liczby użytkowników. W wersji update 1.2 większość użytkowników StorSimple zobaczy następujące nowe funkcje i ulepszenia:

* **Migracja z serii 5000-7000 do urządzeń z serii 8000** — w tej wersji wprowadzono nową funkcję migracji, która umożliwia użytkownikom urządzeń z serii StorSimple 5000-7000 migrację danych do urządzenia z serii StorSimple 8000 lub urządzenia wirtualnego. Funkcja migracji ma dwie kluczowe propozycje wartości:                                                                  
  
  * **Ciągłość działania**, umożliwiając migrację istniejących danych na urządzeniach serii 5000-7000 do urządzeń serii 8000.
  * **Ulepszona oferta funkcji urządzeń z serii 8000,** taka jak efektywne scentralizowane zarządzanie wieloma urządzeniami za pośrednictwem usługi StorSimple Manager, lepsza klasa sprzętu i zaktualizowanego oprogramowania układowego, urządzeń wirtualnych, mobilności danych i funkcji w przyszłości.
    
    Szczegółowe informacje na temat migracji serii StorSimple 5000-7000 do urządzenia z serii 8000 można znaleźć w przewodniku po [migracji.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) 
* **Dostępność w portalu Azure Government Portal** — StorSimple jest teraz dostępna w portalu Azure Government. Zobacz, jak [wdrożyć urządzenie StorSimple w portalu Azure Government Portal](storsimple-deployment-walkthrough-gov.md).
* **Obsługa innych dostawców usług w chmurze** — inni dostawcy usług w chmurze obsługiwane są Amazon S3, Amazon S3 z RRS, HP i OpenStack (beta).
* **Aktualizacja do najnowszych interfejsów API magazynu** — w tej wersji StorSimple został zaktualizowany do najnowszych interfejsów API usługi Azure Storage. Urządzenia z serii StorSimple 8000 z uruchomioną wersją oprogramowania przed aktualizacją 1 (wersja 0.1, 0.2 i 0.3) używają wersji interfejsów API usługi Azure Storage service starszych niż 17 lipca 2009 r. Jak stwierdzono w zaktualizowanym [ogłoszeniu o usunięciu wersji usługi storage,](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)do 1 sierpnia 2016 r. te interfejsy API zostaną przestarzałe. Konieczne jest zastosowanie aktualizacji storsimple serii 8000 1 przed 1 sierpnia 2016 r. Jeśli to się nie uda, urządzenia StorSimple przestaną działać poprawnie.
* **Obsługa strefowej nadmiarowej pamięci masowej (ZRS)** — wraz z uaktualnieniem do najnowszej wersji interfejsów API magazynu seria StorSimple 8000 będzie obsługiwać strefową nadmiarową pamięć masową (ZRS) oprócz magazynu nadmiarowego lokalnie (LRS) i magazynu geograficznie nadmiarowego (GRS). Zapoznaj się z tym [artykułem na temat opcji nadmiarowości usługi Azure Storage,](../storage/common/storage-redundancy.md) aby uzyskać szczegółowe informacje.
* **Ulepszone środowisko początkowego wdrażania i aktualizacji** — w tej wersji ulepszono procesy instalacji i aktualizacji. Instalacja za pomocą kreatora konfiguracji jest ulepszona, aby przekazać opinię użytkownikowi, jeśli ustawienia konfiguracji sieci i zapory są nieprawidłowe. Dodatkowe polecenia cmdlet diagnostyczne zostały dostarczone, aby ułatwić rozwiązywanie problemów z siecią urządzenia. Zobacz [artykuł dotyczące wdrażania rozwiązywania problemów,](storsimple-troubleshoot-deployment.md) aby uzyskać więcej informacji na temat nowych poleceń cmdlet diagnostycznych używanych do rozwiązywania problemów.

## <a name="issues-fixed-in-update-12"></a>Problemy rozwiązane w aktualizacji 1.2
Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacjach 1.2, 1.1 i 1.    

| Nie. | Funkcja | Problem | Naprawiono w aktualizacji | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell for StorSimple |Gdy użytkownik zdalnie uzyskał dostęp do urządzenia StorSimple przy użyciu programu Windows PowerShell for StorSimple, a następnie uruchomił kreatora konfiguracji, wystąpiła awaria zaraz po wprowadzeniu adresu IP danych 0. Ten błąd został naprawiony w aktualizacji 1. |Update 1 |Tak |Tak |
| 2 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach po przymknięciu ustawień fabrycznych urządzenie StorSimple utknęło i wyświetliło ten komunikat: **Resetowanie do ustawień fabrycznych jest w toku (faza 8).** Stało się tak, jeśli naciśnięty klawisz CTRL +C podczas polecenia cmdlet był w toku. Ten błąd został naprawiony. |Update 1 |Tak |Nie |
| 3 |Resetowanie do ustawień fabrycznych |Po nieudanym przymknąć fabrykę dwóch kontrolerów, pozwolono na kontynuowanie rejestracji urządzenia. Spowodowało to nieobsługiwał konfiguracji systemu. W aktualizacji 1 wyświetlany jest komunikat o błędzie, a rejestracja jest blokowana na urządzeniu, które ma nieudane przywrócenie ustawień fabrycznych. |Update 1 |Tak |Nie |
| 4 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach zostały zgłoszone alerty niezgodności fałszywie dodatnie. Niepoprawne alerty niezgodności nie będą już generowane na urządzeniach z aktualizacją 1. |Update 1 |Tak |Nie |
| 5 |Resetowanie do ustawień fabrycznych |Jeśli przywrócenie ustawień fabrycznych zostało przerwane przed zakończeniem, urządzenie weszło w tryb odzyskiwania i nie zezwoliło na dostęp do programu Windows PowerShell for StorSimple. Ten błąd został naprawiony. |Update 1 |Tak |Nie |
| 6 |Odzyskiwanie po awarii |Naprawiono błąd odzyskiwania po awarii (DR), w którym odzyskiwania po awarii podczas odnajdywania kopii zapasowych na urządzeniu docelowym. |Update 1 |Tak |Tak |
| 7 |Diody LED do monitorowania |W niektórych przypadkach monitorowanie diod LED z tyłu urządzenia nie wskazywało prawidłowego stanu. Niebieska dioda LED została wyłączona. Diody LED DATA 0 i DATA 1 migały nawet wtedy, gdy te interfejsy nie były skonfigurowane. Problem został rozwiązany, a diody LED monitorujące wskazują teraz prawidłowy stan. |Update 1 |Tak |Nie |
| 8 |Diody LED do monitorowania |W niektórych przypadkach po zastosowaniu aktualizacji 1 niebieskie światło na aktywnym kontrolerze wyłączyło się, utrudniając zidentyfikowanie aktywnego kontrolera. Ten problem został rozwiązany w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Nie |
| 9 |Interfejsy sieciowe |W poprzednich wersjach urządzenie StorSimple skonfigurowane z bramą nierutowalną może przejść do trybu offline. W tej wersji metryka routingu dla danych 0 została wykonana jako najniższa; w związku z tym, nawet jeśli inne interfejsy sieciowe są włączone w chmurze, cały ruch w chmurze z urządzenia będą kierowane za pośrednictwem danych 0. |Update 1 |Tak |Tak |
| 10 |Tworzenie kopii zapasowych |Błąd w aktualizacji 1, który spowodował niepowodzenie kopii zapasowych po 24 dniach, został naprawiony w aktualizacji 1.1 wydania poprawki. |Aktualizacja 1.1 |Tak |Tak |
| 11 |Tworzenie kopii zapasowych |Błąd w poprzednich wersjach powodował niską wydajność migawek w chmurze przy niskich szybkościach zmian. Ten błąd został naprawiony w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Tak |
| 12 |Aktualizacje |W tej wersji poprawki naprawiono błąd w aktualizacji 1, który zgłosił nieudane uaktualnienie i spowodował przejście kontrolerów w tryb odzyskiwania. |Aktualizacja 1.2 |Tak |Tak |

## <a name="known-issues-in-update-12"></a>Znane problemy w aktualizacji 1.2
Poniższa tabela zawiera podsumowanie znanych problemów w tej wersji.

| Nie. | Funkcja | Problem | Komentarze/obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowy EBOD urządzenia 8600 są odłączone, co powoduje brak kworum dysku, a następnie puli magazynu będzie w trybie offline. Pozostanie w trybie offline, nawet jeśli dyski zostaną ponownie podłączone. |Musisz ponownie uruchomić urządzenie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |Tak |Nie |
| 2 |Nieprawidłowy identyfikator kontrolera |Po wykonaniu wymiany kontrolera kontroler 0 może pojawić się jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może być początkowo pokazywał się jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach to zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Ta sytuacja rozwiąże się po zakończeniu wymiany kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Za pomocą usługi magazynowania, aby usunąć konto magazynu jest nieobsługiwał scenariusz. Doprowadzi to do sytuacji, w której nie można pobrać danych użytkownika. |Tak |Tak | |
| 4 |Przewijowanie awaryjne urządzenia |Wiele przechyłów awaryjnych kontenera woluminu z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwany. Przewijanie awaryjne urządzenia z jednego martwego urządzenia na wiele urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu awaryjnym utracą własność danych. Po takiej pracy awaryjnej te kontenery woluminów będą wyświetlane lub zachowywać się inaczej podczas wyświetlania ich w klasycznej witrynie Azure portal. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji karty StorSimple dla programu SharePoint należy podać adres IP urządzenia, aby instalacja zakończyła się pomyślnie. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma https jako określony protokół, komunikacja między urządzeniami zostanie naruszona, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej będą również generowane w procesie, zużywając znaczne zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma http jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Tak |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurujesz i włączysz serwer proxy sieci Web na zarejestrowanym urządzeniu, musisz ponownie uruchomić aktywny kontroler na urządzeniu. | |Tak |Nie |
| 8 |Duże opóźnienia w chmurze i duże obciążenie we/wy |Gdy urządzenie StorSimple napotka kombinację bardzo dużych opóźnień w chmurze (kolejność sekund) i wysokiego obciążenia we/wy, woluminy urządzenia przechodzą w stan pogorszenia, a we/wy mogą zakończyć się niepowodzeniem z błędem "urządzenie nie jest gotowe". |Należy ręcznie ponownie uruchomić kontrolery urządzeń lub wykonać pracy awaryjnej urządzenia, aby odzyskać z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |Korzystając z polecenia cmdlet StorSimple **Get-AzureStorSimpleStorageAccredential &#124; Select-Object -First 1 -Wait,** aby zaznaczyć pierwszy obiekt, aby można było utworzyć nowy obiekt **VolumeContainer,** polecenie cmdlet zwraca wszystkie obiekty. |Zawijanie polecenia cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Tak |Tak |
| 10 |Migracja |Gdy wiele kontenerów woluminów są przekazywane do migracji, ETA dla najnowszej kopii zapasowej jest dokładne tylko dla pierwszego kontenera woluminu. Ponadto migracja równoległa rozpocznie się po migrowanie pierwszych 4 kopii zapasowych w pierwszym kontenerze woluminu. |Zaleca się migrację jednego kontenera woluminów naraz. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad tworzenia kopii zapasowych ani do grupy dysków wirtualnych. |Aby utworzyć kopie zapasowe, należy dodać te woluminy do zasad tworzenia kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzenie serii 5000/7000 nie może uzyskać dostępu do zmigrowanych kontenerów danych. |Zaleca się usunięcie zmigrowanych kontenerów danych po zakończeniu migracji i zaajsnaniu. |Tak |Nie |
| 13 |Klon i dr |Urządzenie StorSimple z aktualizacją 1 nie może sklonować ani wykonać odzyskiwania po awarii na urządzeniu z uruchomionym oprogramowaniem przed aktualizacją 1. |Należy zaktualizować urządzenie docelowe do aktualizacji 1, aby umożliwić te operacje |Tak |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji dla migracji może zakończyć się niepowodzeniem na urządzeniu z serii 5000-7000, gdy istnieją grupy woluminów bez skojarzonych woluminów. |Usuń wszystkie puste grupy woluminów bez skojarzonych woluminów, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Tak |Nie |

## <a name="physical-device-updates-in-update-12"></a>Aktualizacje urządzeń fizycznych w aktualizacji 1.2
Jeśli aktualizacja poprawki 1.2 zostanie zastosowana do urządzenia fizycznego (wersje uruchomione przed aktualizacją 1), wersja oprogramowania zmieni się na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Aktualizacje kontrolera i oprogramowania układowego w aktualizacji 1.2
Ta wersja aktualizuje sterownik i oprogramowanie układowe dysku na urządzeniu.

* Aby uzyskać więcej informacji na temat aktualizacji kontrolera sygnatury dostępu Współdzielonego, zobacz [Aktualizacja 1 dla kontrolerów LSI SAS w usłudze Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 
* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego dysku, zobacz [Aktualizacja oprogramowania układowego dysku 1 dla urządzenia Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 1.2
Tej aktualizacji nie można zastosować do urządzenia wirtualnego. Konieczne będzie utworzenie nowych urządzeń wirtualnych. 

## <a name="next-steps"></a>Następne kroki
* [Zainstaluj aktualizację 1.2 na swoim urządzeniu](storsimple-install-update-1.md).

