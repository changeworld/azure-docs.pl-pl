---
title: Informacje o wersji StorSimple 8000 serii aktualizacji 1.2 | Dokumentacja firmy Microsoft
description: Opisuje nowe funkcje, problemy i rozwiązania StorSimple 8000 serii aktualizacji 1.2.
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
ms.openlocfilehash: 67603c04cf7b5f45517d16620e5b79241aaae94d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057965"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Zaktualizuj informacje o wersji 1.2 dla danego urządzenia z serii StorSimple 8000

## <a name="overview"></a>Przegląd
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować problemy krytyczne Otwórz StorSimple 8000 serii aktualizacji 1.2. Zawierają one również listę oprogramowania StorSimple, sterowników i aktualizacje oprogramowania układowego dysku zawarte w tej wersji. 

1.2 aktualizację można zastosować do dowolnego urządzenia StorSimple uruchomiona wersja (GA), Update 0.1, Update 0.2 lub Update 0.3 oprogramowania. Aktualizacja 1.2 nie jest dostępna, jeśli urządzenie działa Update 1 lub Update 1.1. Jeśli urządzenie korzysta z wersji (GA), skontaktuj się z [skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) pomocne podczas instalowania tej aktualizacji.

W poniższej tabeli wymieniono wersje oprogramowania urządzenia odpowiadające aktualizacji 1, 1.1 i 1.2.

| Jeśli uruchomiona aktualizacji... | jest to wersja oprogramowania z urządzenia. |
| --- | --- |
| Aktualizacja 1.2 |6.3.9600.17584 |
| Aktualizacja 1.1 |6.3.9600.17521 |
| Update 1.0 |6.3.9600.17491 |

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple. Aby uzyskać więcej informacji, zobacz temat jak [zainstalować 1.2 aktualizacji na urządzeniu StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Trwa około 5 – 10 godzin, aby zainstalować tę aktualizację (w tym aktualizacje systemu Windows). 
> * Aktualizacja 1.2 ma oprogramowania LSI sterowników i aktualizacje oprogramowania układowego dysku. Aby zainstalować, postępuj zgodnie z instrukcjami [zainstalować 1.2 aktualizacji na urządzeniu StorSimple](storsimple-install-update-1.md).
> * Dostępność nowych wersji nie może wyświetlić aktualizacje od razu, ponieważ jak etapowego wdrażania aktualizacji. Skanowanie w poszukiwaniu aktualizacji ponownie za kilka dni one zostanie wkrótce udostępnione.
> 
> 

## <a name="whats-new-in-update-12"></a>Nowości w aktualizacji 1.2
Te funkcje pierwszy zostały wydane z Update 1, która została udostępniona do określonych użytkowników. W wersji 1.2 aktualizacji większość użytkowników StorSimple zobaczyć następujące nowe funkcje i ulepszenia:

* **Migracja z serii 5000 7000 do urządzeń z serii 8000** — tej wersji wprowadzono nową funkcję migracji umożliwiający StorSimple 5000 7000 serii urządzenia migracji ich danych do urządzenia fizycznego z serii StorSimple 8000 lub wirtualny urządzenia. Funkcja migracji ma dwa propozycje wartość klucza:                                                                  
  
  * **Ciągłość prowadzenia działalności biznesowej**, włączając migracji istniejących danych na urządzeniach serii 5000 7000 do urządzeń z serii 8000.
  * **Ulepszona funkcja ofert urządzeń serii 8000**, takich jak wydajność scentralizowane zarządzanie wielu urządzeń przy użyciu usługi Menedżer StorSimple, lepiej klasę urządzeń i aktualizacji oprogramowania układowego, urządzenie wirtualne, mobilności danych Funkcje i w przyszłości plan.
    
    Zapoznaj się [Przewodnik po migracji](http://www.microsoft.com/download/details.aspx?id=47322) dla szczegółowych informacji na temat migracji StorSimple serii 5000 7000 do urządzenia serii 8000. 
* **Dostępność w portalu Azure dla instytucji rządowych** — StorSimple jest teraz dostępna w portalu Azure dla instytucji rządowych. Zobacz temat jak [wdrażania urządzenia StorSimple w portalu Azure dla instytucji rządowych](storsimple-deployment-walkthrough-gov.md).
* **Obsługę innych dostawców usług w chmurze** — innych dostawców usługi w chmurze obsługiwane są Amazon S3, Amazon S3 z RRS, HP i OpenStack (beta).
* **Aktualizacja do najnowszej API magazynu** — w tej wersji StorSimple została zaktualizowana do najnowszej interfejsów API usługi Magazyn Azure. Urządzeń z serii StorSimple 8000 z wersjami oprogramowania przed aktualizacją 1 (wersja, 0.1, 0.2 i 0,3) są używane wersje starsze niż 17 lipca 2009 API usługi magazynu Azure. Jak już wspomniano w zaktualizowanego [powiadomienia o usunięciu wersje usług magazynu](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), przez 1 sierpnia 2016 roku zostaną wycofane te interfejsy API. Konieczne jest stosowanie StorSimple 8000 serii Update 1 przed 1 sierpnia 2016 r. W przypadku awarii to zrobić, urządzenia StorSimple przestanie działać poprawnie.
* **Obsługuje dla strefy nadmiarowego magazynu (ZRS)** — uaktualnienie do najnowszej wersji interfejsów API magazynu serii StorSimple 8000 będzie obsługiwał strefy nadmiarowego magazynu (ZRS) oprócz lokalnie nadmiarowego magazynu (LRS) i magazynu geograficznie nadmiarowego (GRS ). Odwołuje się do tego [artykuł na temat usługi Azure Storage nadmiarowość opcji](../storage/common/storage-redundancy.md) szczegółowe ZRS.
* **Ulepszone początkowej środowisko wdrożenia i zaktualizuj** — w tej wersji zostały rozszerzone procesy instalacji i aktualizacji. Aby przekazać opinię do użytkownika, jeśli konfiguracja sieci i ustawienia zapory są niepoprawne zwiększona instalacji za pomocą Kreatora instalacji. Aby ułatwić rozwiązywanie problemów z siecią urządzenia zostały dołączone dodatkowych diagnostyczne poleceń cmdlet. Zobacz [Rozwiązywanie problemów z artykułu wdrożenia](storsimple-troubleshoot-deployment.md) uzyskać więcej informacji o nowych poleceń cmdlet diagnostycznych, używanych do rozwiązywania problemów.

## <a name="issues-fixed-in-update-12"></a>Problemy, które usunięto w wersji 1.2 aktualizacji
Poniższa tabela zawiera podsumowanie problemów, które zostały usunięte w aktualizacjach 1.2, 1.1 i 1.    

| Nie. | Cecha | Problem | Naprawiono w aktualizacji | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Program Windows PowerShell dla StorSimple |Gdy użytkownik uzyskać zdalny dostęp urządzenia StorSimple przy użyciu programu Windows PowerShell dla urządzenia StorSimple, a następnie uruchomić Kreatora instalacji, tak szybko, jak dane 0 wejściowym IP wystąpił awarii. Ten błąd jest teraz usunięto w wersji Update 1. |Update 1 |Yes |Yes |
| 2 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach po wykonaniu Resetowanie do ustawień fabrycznych urządzenia StorSimple zostało zatrzymane i wyświetlony ten komunikat: **resetowania z fabryką jest w toku (faza 8)**. To się to zdarzyć, jeśli naciśnięciu klawiszy CTRL + C, gdy polecenie cmdlet jest w toku. Ten problem został rozwiązany teraz. |Update 1 |Yes |Nie |
| 3 |Resetowanie do ustawień fabrycznych |Po zresetowaniu fabryki nie powiodło się dwie kontrolera, zostały można przejść do rejestracji urządzeń. Spowodowała nieobsługiwany system konfiguracji. W pakiecie Update 1 jest wyświetlany komunikat o błędzie i rejestracji jest zablokowany na urządzeniu, że ma fabryki nie powiodło się resetowanie. |Update 1 |Yes |Nie |
| 4 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach zostały zgłoszone alerty niezgodność dodatnią wartość false. Niezgodność niepoprawne już nie będą generowane alerty na urządzeniach z aktualizacją Update 1. |Update 1 |Yes |Nie |
| 5 |Resetowanie do ustawień fabrycznych |Jeśli resetowania do ustawień fabrycznych zostało przerwane przed ukończeniem, urządzenie przełączony w tryb odzyskiwania i nie zezwala na dostęp do programu Windows PowerShell dla urządzenia StorSimple. Ten problem został rozwiązany teraz. |Update 1 |Yes |Nie |
| 6 |Odzyskiwanie po awarii |Usterki odzyskiwanie po awarii został rozwiązany, którym odzyskiwania po awarii nie powiedzie się podczas odnajdowania kopii zapasowych na urządzeniu docelowym. |Update 1 |Yes |Yes |
| 7 |Monitorowanie LED |W niektórych przypadkach monitorowania LED z tyłu urządzenia nie poinformował prawidłowy stan. Niebieski LED został wyłączony. DANE 0 i LED 1 dane zostały miga, nawet gdy te interfejsy nie skonfigurowano. Problem został rozwiązany i monitorowania LED teraz wskazuje prawidłowy stan. |Update 1 |Yes |Nie |
| 8 |Monitorowanie LED |W niektórych przypadkach po zastosowaniu aktualizacji 1, niebieski światła na aktywnym kontrolerze wyłączone umożliwiając trudne do zidentyfikowania active kontrolera. Ten problem został rozwiązany w tej wersji poprawki. |Aktualizacja 1.2 |Yes |Nie |
| 9 |Interfejsy sieciowe |W poprzednich wersjach urządzenia StorSimple skonfigurowany bez obsługi routingu bramy można umieścić w trybie offline. W tej wersji został routingu metrykę interfejsu dane 0 wprowadzone najniższy; w związku z tym nawet jeśli inne interfejsy sieciowe są włączone w chmurze, cały ruch chmury z urządzenia zostaną przesłane za pośrednictwem interfejsu dane 0. |Update 1 |Yes |Yes |
| 10 |Tworzenie kopii zapasowych |Błąd w aktualizacji 1, który spowodował kopie zapasowe, aby zakończyć się niepowodzeniem po 24 dni został rozwiązany w wersji 1.1 aktualizacji poprawki. |Aktualizacja 1.1 |Yes |Yes |
| 11 |Tworzenie kopii zapasowych |Błąd w poprzednich wersjach spowodowało pogorszenie wydajności dla migawki w chmurze zmiany niskiej szybkości przetwarzania. Ten problem został rozwiązany w tej wersji poprawki. |Aktualizacja 1.2 |Yes |Yes |
| 12 |Aktualizacje |Błąd w aktualizacji 1, zgłosił nieudanego uaktualnienia, który spowodował kontrolerów, aby przejść do trybu odzyskiwania, został usunięty w tej wersji poprawki. |Aktualizacja 1.2 |Yes |Yes |

## <a name="known-issues-in-update-12"></a>Znane problemy w aktualizacji 1.2
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarz/obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeżeli większość dysków w obudowie EBOD urządzenia 8600 są odłączone spowodować, że nie ma dysku kworum, następnie puli magazynu będzie w trybie offline. Pozostanie on w trybie offline nawet wtedy, gdy dyski są podłączeni ponownie. |Należy ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z Microsoft Support, dalsze czynności. |Yes |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Po wykonaniu zastąpić kontrolera kontrolera 0 mogą wyglądać jak kontrolera 1. Podczas wymiany kontrolera podczas ładowania obrazu z węzła równorzędnego identyfikator kontrolera można wyświetlane początkowo jako identyfikator kontrolera elementu równorzędnego. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Sytuacja ta zostanie rozwiązany samoczynnie po zakończeniu zastąpienia kontrolera. |Yes |Nie |
| 3 |Konta magazynu |Korzystanie z usługi Magazyn można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. |Yes |Yes | |
| 4 |Urządzenia trybu failover |Wiele przechodzenia kontener woluminów z tego samego urządzenia źródłowego na urządzeniach inny element docelowy nie jest obsługiwane. Kontenery woluminów na pierwszym przejścia w tryb failover urządzeń utratę danych własność spowoduje, że urządzenia w tryb failover z jednym urządzeniu martwy na wielu urządzeniach. Po przełączeniu te kontenery woluminów są wyświetlane lub zachowywać się inaczej, podczas wyświetlania w klasycznym portalu Azure. | |Yes |Nie |
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

## <a name="physical-device-updates-in-update-12"></a>Urządzenie fizyczne aktualizacji w 1.2 aktualizacji
Jeśli aktualizacja poprawki 1.2 jest stosowane do urządzenia fizycznego (wersjami przed Update 1), wersji oprogramowania zostanie zmieniony na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Kontroler aktualizacje w 1.2 aktualizacji
Ta wersja aktualizacje sterowników i oprogramowania układowego dysku na urządzeniu.

* Aby uzyskać więcej informacji na temat aktualizacji kontrolera SAS, zobacz [aktualizacji 1 dla kontrolerów LSI SAS w Microsoft Azure StorSimple urządzenia](https://support.microsoft.com/kb/3043005). 
* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego dysku, zobacz [oprogramowania układowego dysku aktualizacji 1 dla programu Microsoft Azure StorSimple urządzenia](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Urządzenie wirtualne aktualizacji w 1.2 aktualizacji
Ta aktualizacja nie można zastosować do urządzenia wirtualnego. Nowe urządzenia wirtualnego będzie muszą zostać utworzone. 

## <a name="next-steps"></a>Kolejne kroki
* [Zainstaluj aktualizację 1.2 na urządzeniu](storsimple-install-update-1.md).

