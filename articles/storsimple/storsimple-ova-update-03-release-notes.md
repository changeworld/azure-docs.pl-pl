---
title: StorSimple Virtual Array Update 0.3 informacje o wersji
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla tablicy wirtualnej StorSimple z aktualizacją 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: f56c36f18379449409f4989eab9510da1f686d0d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397807"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0.3 informacje o wersji
## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy z aktualizacjami tablicy wirtualnej Usługi Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem tablicy wirtualnej StorSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 0.3 odpowiada wersji oprogramowania **10.0.10288.0**.

> [!NOTE]
> Aktualizacje są destrukcyjne i uruchom ponownie urządzenie. Jeśli we/wy są w toku, urządzenie ponosi przestoje.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Co nowego w aktualizacji 0.3
Aktualizacja 0.3 to przede wszystkim kompilacja poprawek. W tej wersji rozwiązano kilka błędów powodujących błędy kopii zapasowej w poprzedniej wersji.

## <a name="issues-fixed-in-the-update-03"></a>Problemy rozwiązane w aktualizacji 0.3
Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych |Wystąpił problem we wcześniejszej wersji, w której kopie zapasowe nie zostaną ukończone dla udziału plików. Jeśli ten problem wystąpił, zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem i alert krytyczny został zgłoszony w usłudze StorSimple Manager, aby powiadomić użytkownika. Ta emisja nie miała wpływu na dane dotyczące udziałów ani na dostęp do danych. Główna przyczyna została zidentyfikowana i ustalona w tej wersji. <br></br> Poprawka nie ma zastosowania z mocą wsteczną do udziałów, które już widzą ten problem. Klienci, którzy widzą ten problem, powinni najpierw zastosować aktualizację 0.3, a następnie skontaktować się z pomocą techniczną firmy Microsoft, aby wykonać pełną kopię zapasową systemu, aby rozwiązać problem. Zamiast kontaktować się z pomocą techniczną firmy Microsoft, klienci mogą również przywrócić nowy udział z dobrej kopii zapasowej dla udziałów, których dotyczy problem. |
| 2 |iSCSI |Problem był widoczny we wcześniejszej wersji, gdzie woluminy znikną podczas kopiowania danych do woluminu na StorSimple Virtual Array. Ten problem został rozwiązany w tej wersji. <br></br> Poprawki są obowiązywać tylko w nowo utworzonych woluminach. Poprawki nie mają zastosowania z mocą wsteczną do woluminów, które już widzą ten problem. Klientom zaleca się przeprowadzenie woluminów, których dotyczy problem, za pośrednictwem klasycznego portalu platformy Azure, wykonanie kopii zapasowej dla tych woluminów, a następnie przywrócenie tych woluminów do nowych woluminów. |

## <a name="known-issues-in-the-update-03"></a>Znane problemy w aktualizacji 0.3
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

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0.3](storsimple-ova-install-update-01.md) na tablicy wirtualnej StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starszej informacji o wydaniu? Przejdź do strony: 

* [StorSimple Virtual Array Update 0.1 i 0.2 Informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [StorSimple Informacje o ogólnej dostępności tablicy wirtualnej](storsimple-ova-pp-release-notes.md)

