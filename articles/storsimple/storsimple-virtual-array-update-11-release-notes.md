---
title: Informacje o wersji 1.1 aktualizacji macierzy wirtualnej StorSimple | Dokumentacja firmy Microsoft
description: Opisuje krytyczne nierozwiązane problemy i rozwiązania StorSimple Virtual Array systemem 1.1 aktualizacji.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789641"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Informacje o wersji 1.1 aktualizacji macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem rozwiązania StorSimple Virtual Array, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 1.1 odnosi się do wersji oprogramowania **10.0.10307.0**.

> [!IMPORTANT]
> - Aktualizacje są naturalnymi i ponownym uruchomieniu urządzenia. W przypadku operacji We/Wy w toku, urządzenie wiąże się z przestojem. Aby uzyskać szczegółowe instrukcje dotyczące sposobu stosowania aktualizacji, przejdź do [instalowanie aktualizacji 1.1](storsimple-virtual-array-install-update-11.md).
>
> - Aktualizacja 1.1 jest dostępne w witrynie Azure portal, tylko wtedy, gdy na urządzeniu działa aktualizację 1.0.

## <a name="whats-new-in-update-11"></a>Co nowego w aktualizacji w wersji 1.1

Ta aktualizacja zawiera następujące rozszerzenie i poprawki błędów:

 - **Utworzyć kopię zapasową błędy** -ulepszono, zwiększając odporność na awarie chmury i wysokie użycie procesora CPU.
 - **Rejestrowanie** — zmiany zostały wprowadzone w trybie informacji pełnej rejestrowania, gdy urządzenie jest w sesji pomocy technicznej.


## <a name="issues-fixed-in-update-11"></a>Problemy rozwiązane w wersji 1.1 aktualizacji

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych| Ta wersja zawiera zmiany, które mają ulepszoną niepowodzeniami tworzenia kopii zapasowych, zwiększając odporność na awarie w chmurze i wysokie użycie procesora CPU.|
| 2 |Rejestrowanie| Ta wersja zawiera zmiany do rejestrowania urządzenia w trakcie sesji pomocy technicznej w trybie informacji pełnej.|


## <a name="known-issues-in-update-11"></a>Znane problemy w wersji 1.1 aktualizacji

Poniższa tabela zawiera podsumowanie informacji o znanych problemach do rozwiązania StorSimple Virtual Array i obejmuje problemy z wymienionych wersji z poprzednich wersji.

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Macierzy wirtualnych utworzonych w wersji zapoznawczej nie można zaktualizować do obsługiwanej wersji ogólnodostępnej. |Te macierze wirtualne muszą Failover dla wersji ogólnodostępnej, za pomocą przepływu pracy odzyskiwania po awarii. |
| **2.** |Dysk aprowizowane dane |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzony odpowiedni macierzy wirtualnej StorSimple, należy nie rozszerzyć lub zmniejszania dysku danych. Takie próby wyniki spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na urządzeniu. |Upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i są do niego zastosowane nie obiekty zasad grupy (GPO). |
| **4.** |Lokalnego internetowego interfejsu użytkownika |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony interfejsu użytkownika lokalnego internetowego, takich jak rozwiązywanie problemów lub konserwacji może nie działać prawidłowo. Przyciski na tych stronach również mogą nie działać. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalnego internetowego interfejsu użytkownika |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze wyświetli 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Warstwowe woluminy lub udziały |Zakres bajtów blokowania dla aplikacji, które działają z usługą StorSimple woluminy warstwowe nie jest obsługiwana. Jeśli jest włączone blokowanie zakresu bajtów, StorSimple obsługi warstw nie działa. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logice aplikacji.<br></br>Wybierz przełączyć woluminy przypięte lokalnie, w przeciwieństwie do woluminów warstwowych dane dla tej aplikacji.<br></br>*Zastrzeżenie:*: Jeśli jest włączone blokowanie zakresu bajtów przy użyciu lokalnie przypięte woluminy, woluminu przypiętego lokalnie może być online, nawet przed zakończeniem przywracania. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Warstwowych udziałów |Praca z dużymi plikami, może spowodować wolne warstwy do zewnątrz. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż 3% rozmiaru udziału. |
| **8.** |Używana pojemność udziałów |Może zostać wyświetlony udostępnić zużycie, gdy nie ma żadnych danych w udziale. To wykorzystania jest używana pojemność udziałów zawiera metadane. | |
| **9.** |Odzyskiwanie po awarii |Można wykonać tylko odzyskiwania po awarii serwera plików do tej samej domeny, jak urządzenie źródłowe. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Jest to zaimplementowane w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu Failover i odzyskiwanie po awarii dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Macierze wirtualne StorSimple nie mogą być zarządzane za pomocą programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem witryny Azure portal i lokalnego Interfejsu w przeglądarce. |
| **11.** |Zmiana hasła |W konsoli urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w en-us za pomocą klawiatury w formacie. | |
| **12.** |PROTOKÓŁ CHAP |Nie można usunąć poświadczeń protokołu CHAP, po utworzeniu. Ponadto w przypadku zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy w tryb offline, a następnie przełącz je na online, aby zmiana zaczęła obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI może różnić się w usłudze Menedżer urządzeń StorSimple i hosta iSCSI. |Hosta iSCSI zawiera widok systemu plików.<br></br>Urządzenie będzie widział bloków, przydzielany, gdy wolumin został w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze zawiera alternatywny Data Stream (ADS) skojarzonych z nim, REKLAM jest nie kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |
| **15.** |Serwer plików |Łącza symbolicznego nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez Windows szyfrowania pliku System (EFS) po skopiowaniu ciągu lub przechowywane na wynik serwera plików rozwiązania StorSimple Virtual Array w nieobsługiwanej konfiguracji.  | |
| **17.** |Aktualizacje |Jeśli zostanie wyświetlony błąd kodu: 2359302 (szesnastkowo 0x240006) podczas próby zainstalowania poprawki przy użyciu lokalnego interfejsu użytkownika, następnie oznacza to, że już zainstalowana na urządzeniu.   | |
| **18.** |Aktualizacje |Jeśli używasz lokalnego internetowego interfejsu użytkownika do zainstalowania aktualizacji 1 na macierz wirtualna, upewnij się, że uruchomieniu aktualizacji Update 0.6. Jeśli używasz nieco niższa niż Update 0.6, należy najpierw zainstalować aktualizacji Update 0.6, a następnie Zastosuj Update 1. Po zainstalowaniu aktualizacji 1.0 bezpośrednio z wersji 0,6 przed aktualizacją, następnie będzie pominąć niektóre aktualizacje, a wykresy monitorowania nie będzie działać.   | |


## <a name="next-steps"></a>Kolejne kroki
[Zainstaluj aktualizację 1.1](storsimple-virtual-array-install-update-11.md) na rozwiązania StorSimple Virtual Array.

## <a name="references"></a>Dokumentacja
Szukasz starszej wersji? Przejdź do strony:
* [StorSimple Virtual Array Update — informacje o wersji 1.0](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 wersji](storsimple-virtual-array-update-06-release-notes.md)
* [Informacje o wersji 0,5 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Informacje o wersji 0,4 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array aktualizacji 0.3 informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji Update macierzy wirtualnej StorSimple 0,1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)
