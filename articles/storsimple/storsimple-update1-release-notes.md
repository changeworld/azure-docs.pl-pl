---
title: Informacje o wersji usługi StorSimple 8000 Series Update 1.2 | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i rozwiązania dla usługi StorSimple 8000 Series Update 1.2.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531021"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Update 1.2 informacje o wersji dla urządzenia StorSimple 8000 series

## <a name="overview"></a>Omówienie
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować krytyczne nierozwiązane problemy dla usługi StorSimple 8000 Series Update 1.2. Zawierają one również listę oprogramowania StorSimple, sterowników i aktualizacje oprogramowania układowego dysku zawartych w tej wersji. 

Aktualizacji Update 1.2 mogą stosowane do każdego urządzenia StorSimple z wersji (GA), aktualizacji 0.1, aktualizacji 0.2 lub oprogramowania Update 0.3. Aktualizacji Update 1.2 nie jest dostępna, jeśli urządzenie działa aktualizacja Update 1 lub Update 1.1. Jeśli na urządzeniu jest uruchomiona wersja (GA), [skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) uzyskanymi zainstalowanie tej aktualizacji.

W poniższej tabeli wymieniono wersje oprogramowania urządzenia odpowiadający aktualizacji 1, 1.1 i 1.2.

| Jeśli uruchomiona aktualizacji... | jest to Twoja wersja oprogramowania urządzenia. |
| --- | --- |
| Aktualizacja 1.2 |6.3.9600.17584 |
| Aktualizacja 1.1 |6.3.9600.17521 |
| Aktualizacja 1.0 |6.3.9600.17491 |

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple. Aby uzyskać więcej informacji, zobacz instrukcje [zainstalować 1.2 aktualizacji na urządzeniu StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Trwa około 5 – 10 godzin, aby zainstalować tę aktualizację (w tym aktualizacji Windows). 
> * Aktualizacji Update 1.2 ma oprogramowania, LSI sterowników i aktualizacje oprogramowania układowego dysku. Aby zainstalować, postępuj zgodnie z instrukcjami [zainstalować 1.2 aktualizacji na urządzeniu StorSimple](storsimple-install-update-1.md).
> * Do nowych wersji, mogą nie być widoczne aktualizacje od razu, ponieważ robimy etapowego wdrażania aktualizacji. Skanowanie w poszukiwaniu aktualizacji w ciągu kilku dni ponownie jako te staną się dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-12"></a>What's new in Update 1.2
Tych funkcji najpierw zostały wydane z Update 1, która została udostępniona ograniczonego zestawu użytkowników. W wersji 1.2 aktualizacji większość użytkowników usługi StorSimple widział następujące nowe funkcje i ulepszenia:

* **Migracja z serii 5000 – 7000 do urządzenia z serii 8000** — w tej wersji wprowadzono nową funkcję migracji, która umożliwia StorSimple 5000 – 7000 series urządzenia migrować swoje dane urządzenie fizyczne StorSimple 8000 series lub wirtualny urządzenie. Funkcja migracji ma dwie wartości kluczy, które:                                                                  
  
  * **Ciągłość prowadzenia działalności biznesowej**, włączając migracji istniejących danych na urządzeniach serii 5000 – 7000 do urządzenia z serii 8000.
  * **Ulepszone oferty funkcji urządzeń z serii 8000**, takich jak wydajne scentralizowane zarządzanie wiele urządzeń przy użyciu usługi StorSimple Manager i lepsze klasy sprzętu i zaktualizować oprogramowanie układowe urządzeń wirtualnych, przesyłanie danych Funkcje i w przyszłości plan.
    
    Zapoznaj się [Przewodnik po migracji](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) Aby uzyskać szczegółowe informacje dotyczące sposobu przeprowadzenia migracji magazynu StorSimple serii 5000 – 7000 do urządzenia z serii 8000. 
* **Dostępność w portalu Azure dla instytucji rządowych** — StorSimple jest teraz dostępna w portalu Azure dla instytucji rządowych. Zobacz jak [wdrożyć urządzenie StorSimple w witrynie Azure Portal dla instytucji rządowych](storsimple-deployment-walkthrough-gov.md).
* **Obsługa innych dostawców usług w chmurze** — innych dostawców usługi w chmurze obsługiwane są Amazon S3, Amazon S3 z RRS, HP i OpenStack (beta).
* **Aktualizacja najnowszych interfejsy API usługi Storage** — w tej wersji StorSimple został zaktualizowany do najnowszej interfejsów API usług Azure Storage. Urządzeń z serii StorSimple 8000, które są uruchomione wersje oprogramowania przed aktualizacją 1 (wersja, 0.1, 0.2 i 0.3) są używane wersje usługi Azure Storage interfejsów API usługi Service starsze niż 17 lipca 2009. Jak wspomniano na zaktualizowanej [ogłoszenie dotyczące usunięcia wersje usług Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), 1 sierpnia 2016 r. te interfejsy API staną się przestarzałe. Konieczne jest zastosowanie StorSimple 8000 Series Update 1 przed 1 sierpnia 2016 r. Jeśli nie to zrobić, urządzeń StorSimple przestać działać poprawnie.
* **Dla magazyn Strefowo nadmiarowy (ZRS) obsługują** — uaktualnienie do najnowszej wersji interfejsów API magazynu serii StorSimple 8000 będzie obsługiwać magazyn Strefowo nadmiarowy (ZRS) oprócz magazyn lokalnie nadmiarowy (LRS) i Magazyn geograficznie nadmiarowy (GRS ). Zapoznaj się z tym [artykuł na temat opcji nadmiarowości usługi Azure Storage](../storage/common/storage-redundancy.md) ZRS szczegółowe informacje.
* **Ulepszone początkowego wdrożenia i zaktualizuj środowisko** — w tej wersji zostały rozszerzone procesów instalacji i aktualizacji. Aby przekazać opinię do użytkownika, jeśli konfiguracja sieci i ustawienia zapory są niepoprawne zwiększona instalacji za pomocą Kreatora instalacji. Dodatkowe polecenia cmdlet diagnostyki zostały dołączone do udzielenia odpowiedzi na rozwiązywanie problemów z siecią urządzenia. Zobacz [wdrożenia w artykule dotyczącym rozwiązywania problemów](storsimple-troubleshoot-deployment.md) Aby uzyskać więcej informacji na temat nowych poleceń cmdlet diagnostycznych używanych do rozwiązywania problemów.

## <a name="issues-fixed-in-update-12"></a>Problemy rozwiązane w Update 1.2
Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji 1.2, 1.1 i 1.    

| Nie. | Cecha | Problem | Naprawiono w aktualizacji | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Program Windows PowerShell dla usługi StorSimple |Gdy użytkownik zdalnie uzyskiwał dostęp do urządzenia StorSimple przy użyciu programu Windows PowerShell dla usługi StorSimple, a następnie uruchomić Kreatora instalacji tak szybko, jak interfejs Data 0 IP określony w parametrze wejściowym wystąpił awarii. Ten problem został rozwiązany w aktualizacji 1. |Update 1 |Tak |Tak |
| 2 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach podczas wykonywania do ustawień fabrycznych urządzenia StorSimple została zablokowana i wyświetlany ten komunikat: **Resetowanie fabryką jest w toku (faza 8)** . Zdarzyło się po naciśnięciu klawiszy CTRL + C, podczas gdy polecenia cmdlet jest w toku. Ten problem został rozwiązany. |Update 1 |Tak |Nie |
| 3 |Resetowanie do ustawień fabrycznych |Po nieudanych podwójną kontrolera Reset do ustawień fabrycznych, zostały może przeprowadzić rejestrację urządzeń. Pozwoliło to odnotować nieobsługiwany system konfiguracji. W programie Update 1 jest wyświetlany komunikat o błędzie i rejestracji jest zablokowany na urządzeniu, że ma nie powiodło się Resetowanie do ustawień fabrycznych. |Update 1 |Tak |Nie |
| 4 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach fałszywie dodatnie niezgodność alerty zostały zgłoszone. Niezgodność niepoprawne alerty nie są już zostanie wygenerowany na urządzeniach z aktualizacją Update 1. |Update 1 |Yes |Nie |
| 5 |Resetowanie do ustawień fabrycznych |W przypadku resetowania do ustawień fabrycznych zostało przerwane przed ukończeniem, urządzenie wprowadzone w tryb odzyskiwania i nie zezwala na dostęp do programu Windows PowerShell dla usługi StorSimple. Ten problem został rozwiązany. |Update 1 |Tak |Nie |
| 6 |Odzyskiwanie po awarii |Usunięto usterkę odzyskiwania po awarii, którym odzyskiwania po awarii może zakończyć się niepowodzeniem podczas odnajdywania kopii zapasowych na urządzeniu docelowym. |Update 1 |Tak |Tak |
| 7 |Diod LED monitorowania |W niektórych przypadkach diod LED monitorowania tyłu urządzenia nie poinformował o prawidłowy stan. Niebieski LED został wyłączony. Interfejs DATA 0 i diod LED 1 danych zostały miga, nawet wtedy, gdy nie zostały skonfigurowane tych interfejsów. Problem został rozwiązany i diod LED monitorowania wskazuje prawidłowy stan. |Update 1 |Tak |Nie |
| 8 |Diod LED monitorowania |W niektórych przypadkach po zastosowaniu aktualizacji 1 niebieski światło na aktywnym kontrolerze wyłączony a tym samym co utrudnia identyfikowanie aktywnego kontrolera. Ten problem został rozwiązany w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Nie |
| 9 |Interfejsy sieciowe |W poprzednich wersjach urządzenia StorSimple skonfigurowany bez obsługi routingu bramy można przejść do trybu offline. W tej wersji zostało routingu metrykę interfejsu dane 0 wprowadzone najniższą; w związku z tym nawet jeśli inne interfejsy sieciowe są włączone w chmurze, cały ruch chmura z urządzenia będą kierowane za pośrednictwem interfejsu dane 0. |Update 1 |Tak |Yes |
| 10 |Tworzenie kopii zapasowych |Błąd w aktualizacji 1, który spowodował kopie zapasowe, aby zakończyć się niepowodzeniem po upływie 24 dni został rozwiązany w wersji 1.1 aktualizacji poprawki. |Aktualizacja 1.1 |Yes |Tak |
| 11 |Tworzenie kopii zapasowych |Usterkę w poprzednich wersjach spowodowało obniżenie wydajności w migawki w chmurze przy użyciu stawek niewielkich zmian. Ten błąd został naprawiony w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Yes |
| 12 |Aktualizacje |Błąd w aktualizacji 1, zgłaszane nieudanego uaktualnienia, który spowodował kontrolerów przejść do trybu odzyskiwania został rozwiązany w tej wersji poprawki. |Aktualizacja 1.2 |Yes |Tak |

## <a name="known-issues-in-update-12"></a>Znane problemy w programie Update 1.2
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze/obejście | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeśli większość dysków w obudowie EBOD się do urządzenia 8600 są odłączone skutkuje dysku kworum, następnie puli magazynu będzie w trybie offline. Pozostanie w trybie offline, nawet wtedy, gdy dyski są zakończone. |Będzie konieczne ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą firmy Microsoft Support dla następnych kroków. |Tak |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Podczas zastępowania kontrolera kontrolera 0 może stanowić kontrolera 1. Podczas zastępowania kontrolera gdy obraz jest ładowany z węzła równorzędnego identyfikator kontrolera było wyświetlane początkowo jako identyfikator kontrolera elementów równorzędnych. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Użytkownik jest wymagana żadna akcja. Ta sytuacja sam się rozwiąże po zakończeniu zastępczego kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Za pomocą usługi Storage można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. |Tak |Yes | |
| 4 |Tryb failover urządzenia |Wielu przejścia w tryb failover kontenera woluminów na tym samym urządzeniu źródła urządzeniom inny element docelowy nie jest obsługiwane. Tryb failover urządzenia z jednego urządzenia martwy do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu w trybie Failover utracić prawa własności do danych. Po włączeniu tych trybu failover te kontenery woluminów wyglądają lub działają inaczej w przypadku można je wyświetlić w klasycznym portalu Azure. | |Tak |Nie |
| 5 |Instalacja |Podczas adaptera StorSimple dla instalacji programu SharePoint musisz podać adres IP urządzenia, w kolejności do pomyślnego ukończenia instalacji. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli w konfiguracji serwera proxy sieci web HTTPS jako określony protokół, komunikacja usługi urządzeń będzie to miało wpływu i urządzenie przejdą w tryb offline. Obsługa pakietów również zostanie wygenerowany w procesie zużywają znaczne zasoby na urządzeniu z systemem. |Upewnij się, że adres URL serwera proxy sieci web ma HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Yes |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurować i włączyć serwer proxy sieci web na zarejestrowanym urządzeniu, należy uruchomić ponownie aktywny kontroler, na urządzeniu. | |Yes |Nie |
| 8 |Czas oczekiwania w chmurze o wysokiej i wysokiego obciążenia We/Wy |Gdy urządzenia StorSimple napotyka chmura bardzo duże opóźnienia (kolejność w sekundach) i wysokiego obciążenia We/Wy, do woluminów urządzenia przechodzi w stan obniżonej wydajności i operacji We/Wy może zakończyć się niepowodzeniem z powodu błędu "urządzenie nie jest gotowy". |Należy ręcznie ponownie uruchomić kontrolery urządzeń lub urządzeń w tryb failover do odzyskania z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |Kiedy używasz polecenia cmdlet usługi StorSimple **Get AzureStorSimpleStorageAccountCredential &#124; Select-Object - najpierw 1 - Wait** do wybrania pierwszego obiektu tak, aby utworzyć nową **elementu VolumeContainer** obiekt, polecenie cmdlet zwraca wszystkie obiekty. |Opakowanie polecenia cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - najpierw 1 - Wait** |Yes |Tak |
| 10 |Migracja |W przypadku wielu kontenerów woluminów są przekazywane do migracji, Inżyniera najnowszej kopii zapasowej jest prawidłowe tylko dla pierwszego kontenera woluminów. Ponadto migracji równoległej rozpocznie się po pierwszych 4 kopie zapasowe w pierwszym kontenera woluminów są migrowane. |Zaleca się migrację jeden kontener woluminów naraz. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad kopii zapasowych lub grupy dysków wirtualnych. |Należy dodać te woluminy do zasad kopii zapasowych w celu tworzenia kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzeń serii 5000 i 7000 nie musi uzyskać dostęp do kontenerów zmigrowanych danych. |Zaleca się usuwanie kontenerów migrowanych danych, po migracji, ukończone i zatwierdzone. |Yes |Nie |
| 13 |Klonuj i odzyskiwanie po awarii |Urządzenia StorSimple z aktualizacją Update 1 nie można sklonować lub odzyskiwanie danych po awarii na urządzeniu z systemem przed aktualizacją oprogramowania 1. |Należy zaktualizować urządzenie docelowe do Update 1, aby zezwolić na te operacje |Tak |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji migracji może zakończyć się niepowodzeniem na urządzeniach serii 5000 – 7000 przypadku grupami woluminów za pomocą skojarzonych woluminów. |Usuń wszystkie grupy pusty wolumin z woluminów skojarzonych, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Yes |Nie |

## <a name="physical-device-updates-in-update-12"></a>Aktualizacje urządzenia fizycznego w Update 1.2
Jeśli zaktualizujesz poprawki 1.2 jest stosowany na urządzeniu fizycznym (uruchomionego w wersjach wcześniejszych niż aktualizacja 1), wersja oprogramowania zostanie zmieniona na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Aktualizacje oprogramowania układowego i kontrolera, w Update 1.2
W tej wersji aktualizacji sterowników i oprogramowania układowego dysku, na urządzeniu.

* Aby uzyskać więcej informacji na temat aktualizacji kontrolera SAS, zobacz [aktualizacji 1 dla kontrolerów LSI SAS w Microsoft Azure Storsimple w](https://support.microsoft.com/kb/3043005). 
* Aby uzyskać więcej informacji dotyczących aktualizacji oprogramowania układowego dysku, zobacz [oprogramowania układowego dysku aktualizacji 1 dla programu Microsoft Azure Storsimple w](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizacje urządzenia wirtualnego w Update 1.2
Ta aktualizacja nie można zastosować do urządzenia wirtualnego. Nowe urządzenia wirtualnego należy ma zostać utworzony. 

## <a name="next-steps"></a>Kolejne kroki
* [Zainstaluj 1.2 aktualizacji na urządzeniu z systemem](storsimple-install-update-1.md).

