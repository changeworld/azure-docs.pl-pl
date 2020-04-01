---
title: StorSimple Virtual Array Update 0.2 & 0.1 informacje o wersji
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla tablicy wirtualnej StorSimple z aktualizacją 0.2 i 0.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 95386f36340aca470769c920e40bbb70e09d34fc
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397880"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0.2 i 0.1 informacje o wersji
## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy z aktualizacjami tablicy wirtualnej Usługi Microsoft Azure StorSimple. (Microsoft Azure StorSimple Virtual Array jest również znany jako StorSimple lokalnego urządzenia wirtualnego lub StorSimple urządzenia wirtualnego.) 

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem urządzenia wirtualnego StorSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 0.2 odpowiada wersji oprogramowania **10.0.10280.0;** Aktualizacja 0.1 jest w wersji **10.0.10279.0**. W poniższych sekcjach znajdziesz listę zmian dla każdej aktualizacji. 

> [!NOTE]
> Aktualizacje są destrukcyjne i uruchomią ponownie urządzenie. Jeśli we/wy są w toku, urządzenie poniesie przestoje.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemy rozwiązane w aktualizacji 0.2
Aktualizacja 0.2 zawiera wszystkie zmiany z aktualizacji 0.1 oprócz poprawki opisanej w poniższej tabeli:

| Funkcja | Problem |
| --- | --- |
| Aktualizacje |W ostatniej wersji aktualizacje nie zostały wykryte automatycznie w klasycznej witrynie Azure portal, więc trzeba było użyć lokalnego interfejsu użytkownika sieci Web, aby zainstalować aktualizacje. Ten problem został rozwiązany w tej wersji. Po zainstalowaniu aktualizacji 0.2 można zainstalować przyszłe aktualizacje przy użyciu klasycznego portalu platformy Azure. |

## <a name="whats-new-in-the-update-01"></a>Co nowego w aktualizacji 0.1
Aktualizacja 0.1 zawiera następujące poprawki i ulepszenia. 

* **Zwiększona odporność na awarie chmury:** Ta wersja ma kilka poprawek błędów dotyczących odzyskiwania po awarii, tworzenia kopii zapasowych, przywracania i warstwowania w przypadku zakłóceń łączności w chmurze. 
* **Zwiększona wydajność przywracania:** Ta wersja ma poprawki błędów, które znacznie skróciły czas ukończenia zadań przywracania.
* **Automatyczna optymalizacja regeneracji miejsca:** Gdy dane są usuwane na woluminach nieobe aprowizacji, nieużywane bloki magazynu muszą zostać odzyskane. Ta wersja usprawniła proces rekultywacji miejsca z chmury, co spowodowało, że nieużywane miejsce stało się dostępne szybciej w porównaniu z poprzednimi wersjami.
* **Nowe obrazy dysków wirtualnych:** nowe dyski VHD, VHDX i VMDK są teraz dostępne za pośrednictwem klasycznego portalu platformy Azure. Możesz pobrać te obrazy, aby aprowizować nowe urządzenia aktualizacji 0.1.
* **Poprawa stanu zadań w portalu**: We wcześniejszej wersji oprogramowania raportowanie stanu zadania w portalu nie było szczegółowe. Ten problem został rozwiązany w tej wersji.
* **Środowisko dołączania do domeny:** poprawki błędów związane z dołączaniem do domeny i ich zmienianiem nazwy.

## <a name="issues-fixed-in-the-update-01"></a>Problemy rozwiązane w aktualizacji 0.1
Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Vmdk |W niektórych wersjach VMware dysk systemu operacyjnego był postrzegany jako rozrzedzony, powodując alerty i zakłócając normalne operacje. Zostało to naprawione w tej wersji. |
| 2 |Serwer iSCSI |W ostatniej wersji użytkownik był zobowiązany do określenia bramy dla każdego włączonego interfejsu sieciowego urządzenia wirtualnego StorSimple. To zachowanie zostało zmienione w tej wersji, dzięki czemu użytkownik musi skonfigurować co najmniej jedną bramę dla wszystkich włączonych interfejsów sieciowych. |
| 3 |Pakiet pomocy technicznej |We wcześniejszej wersji oprogramowania kolekcja pakietów pomocy technicznej nie powiodła się, gdy rozmiary pakietów były większe niż 1 GB. Ten problem został rozwiązany w tej wersji. |
| 4 |Dostęp do chmury |W ostatniej wersji, jeśli StorSimple Virtual Array nie ma łączności sieciowej i został ponownie uruchomiony, lokalny interfejs użytkownika będzie miał problemy z łącznością. Ten problem został rozwiązany w tej wersji. |
| 5 |Monitorowanie wykresów |W poprzedniej wersji, po pracy awaryjnej urządzenia, wykresy wykorzystania pojemności chmury wyświetlane niepoprawne wartości w klasycznej witryny azure portalu. Jest to ustalone w bieżącym wydaniu. |

## <a name="known-issues-in-the-update-01"></a>Znane problemy w aktualizacji 0.1
Poniższa tabela zawiera podsumowanie znanych problemów dla StorSimple Virtual Array i zawiera problemy z wersji zauważyć z poprzednich wersji. **Wydanie problemów odnotowane w tej wersji jest oznaczone gwiazdką. Prawie wszystkie problemy na tej liście zostały przeniesione z wersji GA StorSimple Virtual Array.**

| Nie. | Funkcja | Problem | Obejście/komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzeń wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualne muszą zostać przejęte awaryjnie dla wydania ogólnej dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Aprowizowany dysk danych |Po zainicjowaniu obsługi administracyjnej dysku danych o określonym rozmiarze i utworzeniu odpowiedniego urządzenia wirtualnego StorSimple nie należy rozszerzać ani zmniejszać dysku danych. Próba wykonania tej tezy spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna znajduje się we własnej jednostce organizacyjnej (OU) dla usługi Active Directory i nie są stosowane do niej żadne obiekty zasad grupy. |
| **4.** |Lokalny interfejs użytkownika w sieci Web |Jeśli w programie Internet Explorer (IE ESC) są włączone ulepszone funkcje zabezpieczeń, niektóre lokalne strony interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie działać poprawnie. Przyciski na tych stronach również mogą nie działać. |Wyłącz rozszerzone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalny interfejs użytkownika w sieci Web |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci web są wyświetlane jako interfejsy 10 Gb/s. |To zachowanie jest odzwierciedleniem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 Gb/s dla wirtualnych kart sieciowych. |
| **6.** |Wolumeny warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji, które pracują z StorSimple woluminów warstwowych nie jest obsługiwany. Jeśli blokada zakresu bajtów jest włączona, StorSimple warstwy nie będzie działać. |Zalecane środki obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz, aby umieścić dane dla tej aplikacji w woluminach przypiętych lokalnie, w przeciwieństwie do woluminów warstwowych.<br></br>*Zastrzeżenie:* Jeśli jest włączone lokalne przypięte woluminy i blokowanie zakresu bajtów, należy pamiętać, że wolumin przypięty lokalnie może być w trybie online jeszcze przed zakończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, następnie należy poczekać na przywrócenie, aby zakończyć. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zaleca się, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **8.** |Wykorzystana pojemność dla akcji |Zużycie udziału może być widoczne w przypadku braku jakichkolwiek danych dotyczących udziału. Jest to spowodowane używane pojemności dla udziałów zawiera metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie co urządzenie źródłowe. Odzyskiwanie po awarii na urządzeniu docelowym w innej domenie nie jest obsługiwane w tej wersji. |Zostanie to zaimplementowane w nowszej wersji. |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeniami wirtualnymi StorSimple za pośrednictwem programu Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinny odbywać się za pośrednictwem klasycznego portalu platformy Azure i lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzeń tablicy wirtualnej akceptuje dane wejściowe tylko w formacie klawiatury w USA. | |
| **12.** |CHAP |Po utworzeniu protokołu CHAP nie można usunąć. Ponadto jeśli zmodyfikujesz poświadczenia protokołu CHAP, należy przetraktować woluminy w trybie offline, a następnie przesuń je do trybu online, aby zmiana została uwzględniona. |Zostaną one omówione w późniejszym wydaniu. |
| **13.** |Serwer iSCSI |"Używany magazyn" wyświetlany dla woluminu iSCSI może być inny w usłudze StorSimple Manager i na hoście iSCSI. |Host iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin był przy maksymalnym rozmiarze. |
| **14.** |Serwer plików* |Jeśli plik w folderze ma alternatywny strumień danych (ADS) skojarzony z nim, ads nie jest archiwizowana lub przywracana za pomocą odzyskiwania po awarii, klonowania i odzyskiwania poziomu elementu. | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizacje](storsimple-ova-install-update-01.md) na tablicy wirtualnej StorSimple.

