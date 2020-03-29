---
title: StorSimple Virtual Array Update 1.1 informacje o wersji| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla tablicy wirtualnej StorSimple z aktualizacją 1.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: 55103d6307614f8796c41c35d6345e1fc3aca261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60789641"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple Virtual Array Update 1.1 informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy z aktualizacjami tablicy wirtualnej Usługi Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem tablicy wirtualnej StorSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 1.1 odpowiada wersji oprogramowania **10.0.10307.0**.

> [!IMPORTANT]
> - Aktualizacje są destrukcyjne i uruchom ponownie urządzenie. Jeśli we/wy są w toku, urządzenie ponosi przestoje. Aby uzyskać szczegółowe instrukcje dotyczące stosowania aktualizacji, przejdź do [instalacji aktualizacji 1.1](storsimple-virtual-array-install-update-11.md).
>
> - Aktualizacja 1.1 jest dostępna za pośrednictwem witryny Azure portal tylko wtedy, gdy na urządzeniu jest uruchomiona aktualizacja 1.0.

## <a name="whats-new-in-update-11"></a>Co nowego w aktualizacji 1.1

Ta aktualizacja zawiera następujące ulepszenia i poprawki błędów:

 - **Błędy kopii zapasowych** — zostały ulepszone przez zwiększenie odporności na awarie chmury i wysokie zużycie procesora CPU.
 - **Rejestrowanie** — wprowadzono zmiany w trybie szczegółowym rejestrowania, gdy urządzenie znajduje się w sesji pomocy technicznej.


## <a name="issues-fixed-in-update-11"></a>Problemy rozwiązane w aktualizacji 1.1

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych| Ta wersja zawiera zmiany, które poprawiły błędy kopii zapasowej, zwiększając odporność na awarie w chmurze i wysokie użycie procesora CPU.|
| 2 |Rejestrowanie| Ta wersja zawiera zmiany w rejestrowaniu, gdy urządzenie znajduje się w sesji pomocy technicznej w trybie pełnej.|


## <a name="known-issues-in-update-11"></a>Znane problemy w aktualizacji 1.1

Poniższa tabela zawiera podsumowanie znanych problemów dla StorSimple Virtual Array i zawiera problemy z wersji zauważyć z poprzednich wersji.

| Nie. | Funkcja | Problem | Obejście/komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Tablic wirtualnych utworzonych w wersji zapoznawczej nie można zaktualizować do obsługiwanej wersji ogólnej dostępności. |Te tablice wirtualne muszą zostać przejęte awaryjnie dla wydania ogólnej dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Aprowizowany dysk danych |Po zainicjowaniu obsługi administracyjnej dysku danych o określonym rozmiarze i utworzeniu odpowiedniej tablicy wirtualnej StorSimple nie należy rozszerzać ani zmniejszać dysku danych. Próba wykonania powoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna znajduje się we własnej jednostce organizacyjnej (OU) dla usługi Active Directory i nie są stosowane do niej żadne obiekty zasad grupy. |
| **4.** |Lokalny interfejs użytkownika w sieci Web |Jeśli w programie Internet Explorer (IE ESC) są włączone ulepszone funkcje zabezpieczeń, niektóre lokalne strony interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie działać poprawnie. Przyciski na tych stronach również mogą nie działać. |Wyłącz rozszerzone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalny interfejs użytkownika w sieci Web |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci web są wyświetlane jako interfejsy 10 Gb/s. |To zachowanie jest odzwierciedleniem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 Gb/s dla wirtualnych kart sieciowych. |
| **6.** |Wolumeny warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji, które pracują z StorSimple woluminów warstwowych nie jest obsługiwany. Jeśli blokada zakresu bajtów jest włączona, StorSimple warstwy nie działa. |Zalecane środki obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz, aby umieścić dane dla tej aplikacji w woluminach przypiętych lokalnie, w przeciwieństwie do woluminów warstwowych.<br></br>*Zastrzeżenie:* W przypadku korzystania z woluminów przypiętych lokalnie i blokowania zakresu bajtów jest włączona, wolumin przypięty lokalnie może być w trybie online, nawet przed zakończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, następnie należy poczekać na przywrócenie, aby zakończyć. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zaleca się, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **8.** |Wykorzystana pojemność dla akcji |Zużycie udziału może być widoczne, gdy nie ma żadnych danych w udziale. To zużycie jest, ponieważ używana pojemność dla udziałów zawiera metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie co urządzenie źródłowe. Odzyskiwanie po awarii na urządzeniu docelowym w innej domenie nie jest obsługiwane w tej wersji. |Jest to realizowane w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu failover i odzyskiwania po awarii dla tablicy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Nie można zarządzać tablicami wirtualnymi StorSimple za pośrednictwem programu Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinny odbywać się za pośrednictwem witryny Azure portal i lokalnego interfejsu użytkownika sieci web. |
| **11.** |Zmiana hasła |Konsola urządzenia tablicy wirtualnej akceptuje dane wejściowe tylko w formacie klawiatury en-us. | |
| **12.** |CHAP |Po utworzeniu protokołu CHAP nie można usunąć. Ponadto jeśli zmodyfikujesz poświadczenia protokołu CHAP, musisz przetraktować woluminy w trybie offline, a następnie przesuń je do trybu online, aby zmiana została uwzględniona. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Używany magazyn" wyświetlany dla woluminu iSCSI może być inny w usłudze StorSimple Device Manager i na hoście iSCSI. |Host iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin był przy maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze ma alternatywny strumień danych (ADS) skojarzony z nim, ads nie jest archiwizowana lub przywracana za pomocą odzyskiwania po awarii, klonowania i odzyskiwania poziomu elementu. | |
| **15.** |Serwer plików |Dowiązania symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system szyfrowania plików systemu Windows (EFS) podczas kopiowania lub przechowywania na serwerze plików StorSimple Virtual Array skutkują nieobsługiwaniem konfiguracji.  | |
| **17.** |Aktualizacje |Jeśli podczas próby zainstalowania poprawki za pośrednictwem lokalnego interfejsu użytkownika jest widoczny kod błędu: 2359302 (hex 0x240006), oznacza to, że poprawka jest już zainstalowana na urządzeniu.   | |
| **18.** |Aktualizacje |Jeśli używasz lokalnego interfejsu użytkownika sieci Web do zainstalowania aktualizacji 1 na tablicy wirtualnej, należy upewnić się, że jest uruchomiona aktualizacja 0.6. Jeśli korzystasz z wersji niższej niż aktualizacja 0.6, należy najpierw zainstalować aktualizację 0.6, a następnie zastosować aktualizację 1. Jeśli bezpośrednio zainstalujesz aktualizację 1.0 z wersji wstępnej 0.6, przegapisz kilka aktualizacji, a wykresy monitorowania nie będą działać.   | |


## <a name="next-steps"></a>Następne kroki
[Zainstaluj aktualizację 1.1](storsimple-virtual-array-install-update-11.md) na tablicy wirtualnej StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starszej informacji o wydaniu? Przejdź do strony:
* [StorSimple Virtual Array Update 1.0 Release Notes](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Informacje o wersji](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Release Notes](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 i 0.2 Informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [StorSimple Informacje o ogólnej dostępności tablicy wirtualnej](storsimple-ova-pp-release-notes.md)
