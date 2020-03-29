---
title: StorSimple Virtual Array Update 0.5 informacje o wersji| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla tablicy wirtualnej StorSimple z aktualizacją 0.5.
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870676"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0.5 informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy z aktualizacjami tablicy wirtualnej Usługi Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem tablicy wirtualnej StorSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 0.5 odpowiada wersji oprogramowania **10.0.10290.0**.

> [!NOTE]
> Aktualizacje są destrukcyjne i uruchom ponownie urządzenie. Jeśli we/wy są w toku, urządzenie ponosi przestoje. Aby uzyskać szczegółowe instrukcje dotyczące stosowania aktualizacji, przejdź do [instalacji aktualizacji 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Co nowego w aktualizacji 0.5
Aktualizacja 0.5 jest przede wszystkim kompilacji naprawić błąd. Główne ulepszenia i poprawki błędów są następujące:

- **Ulepszenia odporności kopii zapasowych** — w tej wersji wprowadzono poprawki, które zwiększają odporność kopii zapasowych. We wcześniejszych wersjach kopie zapasowe zostały ponowione tylko dla niektórych wyjątków. Ta wersja ponawia wszystkie wyjątki kopii zapasowej i sprawia, że kopie zapasowe bardziej odporne.

- **Aktualizacje monitorowania użycia magazynu** — od 30 czerwca 2017 r. monitorowanie użycia magazynu dla serii urządzeń wirtualnych StorSimple zostanie wycofane. Dotyczy to wykresów monitorowania na wszystkich tablicach wirtualnych z aktualizacją 0.4 lub niższą. Ta aktualizacja zawiera zmiany wymagane do kontynuowania korzystania z monitorowania użycia magazynu w witrynie Azure portal. Zainstaluj tę krytyczną aktualizację przed 30 czerwca 2017 r., aby kontynuować korzystanie z funkcji monitorowania.


## <a name="issues-fixed-in-the-update-05"></a>Problemy rozwiązane w aktualizacji 0.5

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Odporność kopii zapasowych| We wcześniejszych wersjach kopie zapasowe zostały ponowione tylko dla niektórych wyjątków. Ta wersja zawiera poprawkę, aby kopie zapasowe były bardziej odporne, ponowiając próbę wszystkich wyjątków kopii zapasowej.|
| 2 |Monitorowanie| Monitorowanie użycia magazynu dla storsimple virtual device series zostanie przestarzałe od 30 czerwca 2017. Ta akcja ma wpływ na wykresy monitorowania w usłudze StorSimple Device Manager uruchomionej w systemach StorSimple Virtual Arrays (model 1200). Ta wersja ma aktualizacje, które umożliwiają użytkownikowi kontynuowanie korzystania z monitorowania użycia magazynu na tablicach wirtualnych po 30 czerwca 2017 r.|
| 3 |Serwer plików| We wcześniejszych wersjach użytkownik może omyłkowo skopiować zaszyfrowane pliki do tablicy wirtualnej. Ta wersja zawiera poprawkę, która nie zezwala na kopiowanie zaszyfrowanych plików do tablicy wirtualnej. Jeśli urządzenie ma już zaszyfrowane pliki przed aktualizacją, kopie zapasowe będą nadal kończą się niepowodzeniem, dopóki wszystkie zaszyfrowane pliki nie zostaną usunięte z systemu. |


## <a name="known-issues-in-the-update-05"></a>Znane problemy w aktualizacji 0.5

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
| **8.** |Wykorzystana pojemność dla akcji |Zużycie udziału może być widoczne, gdy nie ma żadnych danych w udziale. To zużycie jest, ponieważ używana pojemność dla udziałów zawiera metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie co urządzenie źródłowe. Odzyskiwanie po awarii na urządzeniu docelowym w innej domenie nie jest obsługiwane w tej wersji. |Jest to realizowane w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu failover i odzyskiwania po awarii dla tablicy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeniami wirtualnymi StorSimple za pośrednictwem programu Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinny odbywać się za pośrednictwem witryny Azure portal i lokalnego interfejsu użytkownika sieci web. |
| **11.** |Zmiana hasła |Konsola urządzenia tablicy wirtualnej akceptuje dane wejściowe tylko w formacie klawiatury en-us. | |
| **12.** |CHAP |Po utworzeniu protokołu CHAP nie można usunąć. Ponadto jeśli zmodyfikujesz poświadczenia protokołu CHAP, musisz przetraktować woluminy w trybie offline, a następnie przesuń je do trybu online, aby zmiana została uwzględniona. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Używany magazyn" wyświetlany dla woluminu iSCSI może być inny w usłudze StorSimple Device Manager i na hoście iSCSI. |Host iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin był przy maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze ma alternatywny strumień danych (ADS) skojarzony z nim, ads nie jest archiwizowana lub przywracana za pomocą odzyskiwania po awarii, klonowania i odzyskiwania poziomu elementu. | |
| **15.** |Serwer plików |Dowiązania symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system szyfrowania plików systemu Windows (EFS) podczas kopiowania lub przechowywania na serwerze plików StorSimple Virtual Array skutkują nieobsługiwaniem konfiguracji.  | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0.5](storsimple-virtual-array-install-update-05.md) na tablicy wirtualnej StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starszej informacji o wydaniu? Przejdź do strony:

* [StorSimple Virtual Array Update 0.4 Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 i 0.2 Informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [StorSimple Informacje o ogólnej dostępności tablicy wirtualnej](storsimple-ova-pp-release-notes.md)

