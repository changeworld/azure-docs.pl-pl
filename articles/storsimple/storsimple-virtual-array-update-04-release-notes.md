---
title: Informacje o wersji 0,4 wirtualnego aktualizacji macierzy StorSimple | Dokumentacja firmy Microsoft
description: Opis macierzy wirtualnej StorSimple z aktualizacją Update Update 0.4 krytyczne nierozwiązane problemy i rozwiązania.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334798"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Informacje o wersji 0,4 StorSimple Update wirtualnego w tablicy

## <a name="overview"></a>Omówienie

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem rozwiązania StorSimple Virtual Array, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja Update 0.4 odnosi się do wersji oprogramowania **10.0.10289.0**.

> [!NOTE]
> Aktualizacje są naturalnymi i ponownym uruchomieniu urządzenia. W przypadku operacji We/Wy w toku, urządzenie wiąże się z przestojem.


## <a name="whats-new-in-the-update-04"></a>Co nowego w aktualizacji Update 0.4
Aktualizacja Update 0.4 to przede wszystkim kompilacji naprawienie usterki, które są połączone z kilka ulepszeń. W tej wersji rozwiązano kilka błędów skutkuje niepowodzeniami tworzenia kopii zapasowych w poprzedniej wersji. Głównych ulepszeń i poprawek błędów są następujące:

- **Wykonaj kopię zapasową ulepszenia wydajności** — w tej wersji podejścia biznesowego uczyniło kilka ulepszeń klucza w celu zwiększenia wydajności tworzenia kopii zapasowych. W rezultacie kopie zapasowe, które obejmują dużą liczbę plików, zobacz znaczny spadek czas wymagany do ukończenia pełnych i przyrostowych kopii zapasowych.

- **Ulepszone wydajność przywracania** — ta wersja zawiera ulepszenia, które znacznie poprawić wydajność przywracania, korzystając z dużą liczbą plików. Przy użyciu 2-4 miliony plików, firma Microsoft zaleca aprowizowania macierzy wirtualnej z 16 GB pamięci RAM, aby zobaczyć ulepszenia. Korzystając z mniej niż 2 miliony plików, minimalnym wymaganiem dla maszyny wirtualnej jest nadal 8 GB pamięci RAM.

- **Ulepszenia pakietu dla pomocy technicznej** — ulepszenia obejmują rejestrowanie statystyki dla dysku, procesora CPU, pamięci, sieci i chmury w pakiecie pomocy technicznej, zwiększając w ten sposób proces diagnozowania/debugowanie problemów z urządzeniami.

- **Limit lokalnie przypięte woluminy iSCSI do 200 GB** — dla woluminów przypiętych lokalnie, zaleca się ograniczenie do woluminu iSCSI 200 GB na rozwiązania StorSimple Virtual Array. Zarezerwowane miejsce lokalne dla woluminów warstwowych w dalszym ciągu mieć 10% aprowizowanego rozmiaru woluminu, ale jest ograniczone do 200 GB. 

- **Poprawki błędów związanych z kopii zapasowej** — poprzednie wersje oprogramowania, wystąpiły problemy związane z kopii zapasowych, które mogą być przyczyną niepowodzenia tworzenia kopii zapasowej. W tej wersji rozwiązano te błędy.


## <a name="issues-fixed-in-the-update-04"></a>Problemy rozwiązane w aktualizacji Update 0.4

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Wydajność tworzenia kopii zapasowej|We wcześniejszych wersjach kopie zapasowe z użyciem dużej liczby plików może zająć dużo czasu do ukończenia (wymienione w kolejności w dniach). W tej wersji zarówno pełnych i przyrostowych kopii zapasowych, zobacz znaczny spadek czas do ukończenia. |
| 2 |Pakiet dla pomocy technicznej|Dysk, procesora CPU, pamięci, sieci i statystyki chmury teraz zalogować się do dzienniki pomocy technicznej, co bardzo skutecznie rozwiązywania wszelkich problemów z urządzeniami pakietów pomocy technicznej.|
| 3 |Backup |We wcześniejszych wersjach długie wykonywanie kopii zapasowych może spowodować Przetwarzaj miejsca na urządzeniu skutkuje niepowodzeniami tworzenia kopii zapasowych. Ta usterka zostanie rozwiązana w tej wersji, umożliwiając nie więcej niż 5 kopii zapasowych do kolejki w tym samym czasie.|
| 4 |iSCSI | We wcześniejszych wersjach zarezerwowane miejsce lokalne woluminy warstwowe lub przypięty lokalnie był 10% aprowizowanego rozmiaru woluminu. W tej wersji jest ograniczona do 10% maksymalnie 200 GB miejsca (w przypadku woluminów warstwowych większych niż 2 TB) zarezerwowane miejsce lokalne dla wszystkich woluminów iSCSI (lokalnie przypięte lub warstwowa) tym samym zwalnianie więcej miejsca na dysku lokalnym. Zaleca się, że woluminy przypięte lokalnie w tej wersji można maksymalnie 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Znane problemy w aktualizacji Update 0.4

Poniższa tabela zawiera podsumowanie informacji o znanych problemach do rozwiązania StorSimple Virtual Array i obejmuje problemy z wymienionych wersji z poprzednich wersji. 

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Urządzenia wirtualne utworzone w wersji zapoznawczej nie można zaktualizować do obsługiwanej wersji ogólnodostępnej. |Te urządzenia wirtualne muszą Failover dla wersji ogólnodostępnej, za pomocą przepływu pracy odzyskiwania po awarii. |
| **2.** |Dysk aprowizowane dane |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzyć odpowiednie urządzenia wirtualnego StorSimple, należy nie rozwinąć lub zmniejszania dysku danych. Takie próby wyniki spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na urządzeniu. |Upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i są do niego zastosowane nie obiekty zasad grupy (GPO). |
| **4.** |Lokalnego internetowego interfejsu użytkownika |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony interfejsu użytkownika lokalnego internetowego, takich jak rozwiązywanie problemów lub konserwacji może nie działać prawidłowo. Przyciski na tych stronach również mogą nie działać. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalnego internetowego interfejsu użytkownika |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze wyświetli 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Warstwowe woluminy lub udziały |Zakres bajtów blokowania dla aplikacji, które działają z usługą StorSimple woluminy warstwowe nie jest obsługiwana. Jeśli jest włączone blokowanie zakresu bajtów, StorSimple obsługi warstw nie działa. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logice aplikacji.<br></br>Wybierz przełączyć woluminy przypięte lokalnie, w przeciwieństwie do woluminów warstwowych dane dla tej aplikacji.<br></br>*Zastrzeżenie:*: Jeśli jest włączone blokowanie zakresu bajtów przy użyciu lokalnie przypięte woluminy, woluminu przypiętego lokalnie może być online, nawet przed zakończeniem przywracania. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Warstwowych udziałów |Praca z dużymi plikami, może spowodować wolne warstwy do zewnątrz. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż 3% rozmiaru udziału. |
| **8.** |Używana pojemność udziałów |Może zostać wyświetlony udostępnić zużycie, gdy nie ma żadnych danych w udziale. Jest to spowodowane używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Można wykonać tylko odzyskiwania po awarii serwera plików do tej samej domeny, jak urządzenie źródłowe. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Jest to zaimplementowane w nowszej wersji. |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeń wirtualnych StorSimple przy użyciu programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem klasycznego portalu Azure i lokalnego Interfejsu w przeglądarce. |
| **11.** |Zmiana hasła |W konsoli urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en US. | |
| **12.** |PROTOKÓŁ CHAP |Nie można usunąć poświadczeń protokołu CHAP, po utworzeniu. Ponadto w przypadku zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy w tryb offline, a następnie przełącz je na online, aby zmiana zaczęła obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą różnić się w usłudze StorSimple Manager i hosta iSCSI. |Hosta iSCSI zawiera widok systemu plików.<br></br>Urządzenie będzie widział bloków, przydzielany, gdy wolumin został w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze zawiera alternatywny Data Stream (ADS) skojarzonych z nim, REKLAM jest nie kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |
| **15.** |Serwer plików |Łącza symbolicznego nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez Windows szyfrowania pliku System (EFS) po skopiowaniu ciągu lub przechowywane na wynik serwera plików rozwiązania StorSimple Virtual Array w nieobsługiwanej konfiguracji.  | |

## <a name="next-step"></a>Następny krok
[Instalowanie aktualizacji Update 0.4](storsimple-virtual-array-install-update-04.md) na rozwiązania StorSimple Virtual Array.

## <a name="references"></a>Dokumentacja
Szukasz starszej wersji? Przejdź do strony: 

* [StorSimple Virtual Array aktualizacji 0.3 informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji Update macierzy wirtualnej StorSimple 0,1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)

