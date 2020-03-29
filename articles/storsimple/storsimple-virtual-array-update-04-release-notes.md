---
title: StorSimple Virtual Array Update 0.4 informacje o wersji| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla tablicy wirtualnej StorSimple z aktualizacją 0.4.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 06a3469507631d032535bce62b01d964e99dc603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60334798"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0.4 informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy z aktualizacjami tablicy wirtualnej Usługi Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem tablicy wirtualnej StorSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 0.4 odpowiada wersji oprogramowania **10.0.10289.0**.

> [!NOTE]
> Aktualizacje są destrukcyjne i uruchom ponownie urządzenie. Jeśli we/wy są w toku, urządzenie ponosi przestoje.


## <a name="whats-new-in-the-update-04"></a>Co nowego w aktualizacji 0.4
Aktualizacja 0.4 to przede wszystkim kompilacja poprawek w połączeniu z kilkoma ulepszeniami. W tej wersji rozwiązano kilka błędów powodujących błędy kopii zapasowej w poprzedniej wersji. Główne ulepszenia i poprawki błędów są następujące:

- **Ulepszenia wydajności kopii zapasowej** — w tej wersji wprowadzono kilka kluczowych ulepszeń w celu zwiększenia wydajności kopii zapasowej. W rezultacie kopie zapasowe, które obejmują dużą liczbę plików, widzą znaczne skrócenie czasu do wykonania, dla pełnych i przyrostowych kopii zapasowych.

- **Zwiększona wydajność przywracania** — ta wersja zawiera ulepszenia, które znacznie zwiększają wydajność przywracania podczas korzystania z dużej liczby plików. Jeśli używasz 2 - 4 milionów plików, zaleca się aprowizowanie tablicy wirtualnej z 16 GB pamięci RAM, aby zobaczyć ulepszenia. W przypadku korzystania z mniej niż 2 milionów plików, minimalne wymagania dla maszyny wirtualnej nadal 8 GB pamięci RAM.

- **Ulepszenia pakietu pomocy technicznej** — ulepszenia obejmują rejestrowanie w statystykach dysku, procesora CPU, pamięci, sieci i chmury do pakietu pomocy technicznej, poprawiając w ten sposób proces diagnozowania/debugowania problemów z urządzeniem.

- **Ogranicz lokalnie przypięte woluminy iSCSI do 200 GB** — w przypadku woluminów przypiętych lokalnie zaleca się ograniczenie do woluminu iSCSI o pojemności 200 GB w macierzy wirtualnej StorSimple. Lokalna rezerwacja woluminów warstwowych nadal wynosi 10 % aprowizowanego rozmiaru woluminu, ale jest ograniczona do 200 GB. 

- **Poprawki błędów związanych z tworzeniem kopii zapasowych** — w poprzednich wersjach oprogramowania wystąpiły problemy związane z kopiami zapasowymi, które powodowałyby błędy tworzenia kopii zapasowych. Te błędy zostały rozwiązane w tej wersji.


## <a name="issues-fixed-in-the-update-04"></a>Problemy rozwiązane w aktualizacji 0.4

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Wydajność tworzenia kopii zapasowych|We wcześniejszych wersjach kopie zapasowe obejmujące dużą liczbę plików zajmie dużo czasu, aby zakończyć (w kolejności dni). W tej wersji zarówno pełne, jak i przyrostowe kopie zapasowe widzą znaczne skrócenie czasu do zakończenia. |
| 2 |Pakiet pomocy technicznej|Statystyki dysku, procesora, pamięci, sieci i chmury są teraz rejestrowane w dziennikach pomocy technicznej, dzięki czemu pakiety pomocy technicznej są bardzo skuteczne w rozwiązywaniu problemów z urządzeniem.|
| 3 |Tworzenie kopii zapasowych |We wcześniejszych wersjach długotrwałe tworzenie kopii zapasowych może spowodować pochyłość miejsca na urządzeniu, co spowoduje błędy kopii zapasowej. Ten błąd został rozwiązany w tej wersji, zezwalając nie więcej niż 5 kopii zapasowych do kolejki w tym czasie.|
| 4 |iSCSI | We wcześniejszych wersjach rezerwacja lokalna dla woluminów warstwowych lub przypiętych lokalnie wynosiła 10% aprowizowanego rozmiaru woluminu. W tej wersji rezerwacja lokalna dla wszystkich woluminów iSCSI (przypiętych lokalnie lub warstwowych) jest ograniczona do 10 % z maksymalnie 200 GB (dla woluminów warstwowych większych niż 2 TB), zwalniając w ten sposób więcej miejsca na dysku lokalnym. Zaleca się, aby woluminy przypięte lokalnie w tej wersji były ograniczone do 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Znane problemy w aktualizacji 0.4

Poniższa tabela zawiera podsumowanie znanych problemów dla StorSimple Virtual Array i zawiera problemy z wersji zauważyć z poprzednich wersji. 

| Nie. | Funkcja | Problem | Obejście/komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzeń wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualne muszą zostać przejęte awaryjnie dla wydania ogólnej dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Aprowizowany dysk danych |Po zainicjowaniu obsługi administracyjnej dysku danych o określonym rozmiarze i utworzeniu odpowiedniego urządzenia wirtualnego StorSimple nie należy rozszerzać ani zmniejszać dysku danych. Próba wykonania powoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna znajduje się we własnej jednostce organizacyjnej (OU) dla usługi Active Directory i nie są stosowane do niej żadne obiekty zasad grupy. |
| **4.** |Lokalny interfejs użytkownika w sieci Web |Jeśli w programie Internet Explorer (IE ESC) są włączone ulepszone funkcje zabezpieczeń, niektóre lokalne strony interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie działać poprawnie. Przyciski na tych stronach również mogą nie działać. |Wyłącz rozszerzone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalny interfejs użytkownika w sieci Web |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci web są wyświetlane jako interfejsy 10 Gb/s. |To zachowanie jest odzwierciedleniem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 Gb/s dla wirtualnych kart sieciowych. |
| **6.** |Wolumeny warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji, które pracują z StorSimple woluminów warstwowych nie jest obsługiwany. Jeśli blokada zakresu bajtów jest włączona, StorSimple warstwy nie działa. |Zalecane środki obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz, aby umieścić dane dla tej aplikacji w woluminach przypiętych lokalnie, w przeciwieństwie do woluminów warstwowych.<br></br>*Zastrzeżenie:* W przypadku korzystania z woluminów przypiętych lokalnie i blokowania zakresu bajtów jest włączona, wolumin przypięty lokalnie może być w trybie online, nawet przed zakończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, następnie należy poczekać na przywrócenie, aby zakończyć. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zaleca się, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **8.** |Wykorzystana pojemność dla akcji |Zużycie udziału może być widoczne, gdy nie ma żadnych danych w udziale. Jest to spowodowane używane pojemności dla udziałów zawiera metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie co urządzenie źródłowe. Odzyskiwanie po awarii na urządzeniu docelowym w innej domenie nie jest obsługiwane w tej wersji. |Jest to realizowane w nowszej wersji. |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeniami wirtualnymi StorSimple za pośrednictwem programu Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinny odbywać się za pośrednictwem klasycznego portalu platformy Azure i lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzeń tablicy wirtualnej akceptuje dane wejściowe tylko w formacie klawiatury w USA. | |
| **12.** |CHAP |Po utworzeniu protokołu CHAP nie można usunąć. Ponadto jeśli zmodyfikujesz poświadczenia protokołu CHAP, musisz przetraktować woluminy w trybie offline, a następnie przesuń je do trybu online, aby zmiana została uwzględniona. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Używany magazyn" wyświetlany dla woluminu iSCSI może być inny w usłudze StorSimple Manager i na hoście iSCSI. |Host iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin był przy maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze ma alternatywny strumień danych (ADS) skojarzony z nim, ads nie jest archiwizowana lub przywracana za pomocą odzyskiwania po awarii, klonowania i odzyskiwania poziomu elementu. | |
| **15.** |Serwer plików |Dowiązania symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system szyfrowania plików systemu Windows (EFS) podczas kopiowania lub przechowywania na serwerze plików StorSimple Virtual Array skutkują nieobsługiwaniem konfiguracji.  | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0.4](storsimple-virtual-array-install-update-04.md) na tablicy wirtualnej StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starszej informacji o wydaniu? Przejdź do strony: 

* [StorSimple Virtual Array Update 0.3 Informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 i 0.2 Informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [StorSimple Informacje o ogólnej dostępności tablicy wirtualnej](storsimple-ova-pp-release-notes.md)

