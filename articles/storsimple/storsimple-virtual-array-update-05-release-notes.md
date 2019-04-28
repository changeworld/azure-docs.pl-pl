---
title: Informacje o wersji 0,5 wirtualnego aktualizacji macierzy StorSimple | Dokumentacja firmy Microsoft
description: Opis macierzy wirtualnej StorSimple z aktualizacją Update 0.5 krytyczne nierozwiązane problemy i rozwiązania.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870676"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Informacje o wersji 0,5 StorSimple Update wirtualnego w tablicy

## <a name="overview"></a>Omówienie

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem rozwiązania StorSimple Virtual Array, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Update 0.5 odnosi się do wersji oprogramowania **10.0.10290.0**.

> [!NOTE]
> Aktualizacje są naturalnymi i ponownym uruchomieniu urządzenia. W przypadku operacji We/Wy w toku, urządzenie wiąże się z przestojem. Aby uzyskać szczegółowe instrukcje dotyczące sposobu stosowania aktualizacji, przejdź do [instalowanie aktualizacji Update 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Co nowego w aktualizacji Update 0.5
Update 0.5 to przede wszystkim poprawki błędów kompilacji. Głównych ulepszeń i poprawek błędów są następujące:

- **Utworzyć kopię zapasową ulepszenia odporności** — ta wersja zawiera poprawki, które zwiększają odporność kopii zapasowej. We wcześniejszych wersjach kopii zapasowych zostały powtórzone tylko dla niektórych wyjątków. Ta wersja ponawia próbę tworzenia kopii zapasowej wyjątki i sprawia, że wykonywanie kopii zapasowych jest bardziej odporne na błędy.

- **Aktualizacje dotyczące monitorowania użycia magazynu** — uruchamianie 30 czerwca 2017 r., monitorowania użycia magazynu dla seria urządzeń wirtualnych StorSimple zostanie wycofana. Dotyczy to wykresy monitorowania na wszystkich tablicach wirtualnego z aktualizacją Update 0,4 lub niższą. Ta aktualizacja zawiera zmiany wymagane do kontynuowania użytkowania monitorowania użycia magazynu w witrynie Azure portal. Zainstaluj tę aktualizację krytyczną przed 30 czerwca 2017 r. Aby kontynuować korzystanie z funkcji monitorowania.


## <a name="issues-fixed-in-the-update-05"></a>Problemy rozwiązane w aktualizacji Update 0.5

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Odporność kopii zapasowej| We wcześniejszych wersjach kopii zapasowych zostały powtórzone tylko dla niektórych wyjątków. Ta wersja zawiera poprawkę się tworzenie kopii zapasowych bardziej odporne przez ponowną próbą wszystkie wyjątki kopii zapasowej.|
| 2 |Monitorowanie| Monitorowanie użycia pamięci masowej dla seria urządzeń wirtualnych StorSimple staną się przestarzałe od 30 czerwca 2017 r. Ta akcja ma wpływ na wykresy monitorowania w usłudze Menedżer urządzeń StorSimple uruchomione na macierzach wirtualnych StorSimple (1200 model). Ta wersja zawiera aktualizacje, które umożliwiają użytkownikom nadal korzystać z monitorowania użycia magazynu w macierzy wirtualnych ponad 30 czerwca 2017.|
| 3 |Serwer plików| We wcześniejszych wersjach użytkownik może przez pomyłkę skopiować zaszyfrowanych plików do macierzy wirtualnej. Ta wersja zawiera poprawkę, która nie pozwala Kopiowanie zaszyfrowanych plików do macierzy wirtualnej. Jeśli urządzenie ma istniejące pliki zaszyfrowane przed aktualizacją, kopie zapasowe będą nadal się niepowodzeniem, dopóki wszystkie zaszyfrowane pliki są usuwane z systemu. |


## <a name="known-issues-in-the-update-05"></a>Znane problemy w aktualizacji Update 0.5

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
| **8.** |Używana pojemność udziałów |Może zostać wyświetlony udostępnić zużycie, gdy nie ma żadnych danych w udziale. To wykorzystania jest używana pojemność udziałów zawiera metadane. | |
| **9.** |Odzyskiwanie po awarii |Można wykonać tylko odzyskiwania po awarii serwera plików do tej samej domeny, jak urządzenie źródłowe. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Jest to zaimplementowane w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu Failover i odzyskiwanie po awarii dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeń wirtualnych StorSimple przy użyciu programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem witryny Azure portal i lokalnego Interfejsu w przeglądarce. |
| **11.** |Zmiana hasła |W konsoli urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w en-us za pomocą klawiatury w formacie. | |
| **12.** |PROTOKÓŁ CHAP |Nie można usunąć poświadczeń protokołu CHAP, po utworzeniu. Ponadto w przypadku zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy w tryb offline, a następnie przełącz je na online, aby zmiana zaczęła obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI może różnić się w usłudze Menedżer urządzeń StorSimple i hosta iSCSI. |Hosta iSCSI zawiera widok systemu plików.<br></br>Urządzenie będzie widział bloków, przydzielany, gdy wolumin został w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze zawiera alternatywny Data Stream (ADS) skojarzonych z nim, REKLAM jest nie kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |
| **15.** |Serwer plików |Łącza symbolicznego nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez Windows szyfrowania pliku System (EFS) po skopiowaniu ciągu lub przechowywane na wynik serwera plików rozwiązania StorSimple Virtual Array w nieobsługiwanej konfiguracji.  | |

## <a name="next-step"></a>Następny krok
[Instalowanie aktualizacji Update 0.5](storsimple-virtual-array-install-update-05.md) na rozwiązania StorSimple Virtual Array.

## <a name="references"></a>Dokumentacja
Szukasz starszej wersji? Przejdź do strony:

* [Informacje o wersji 0,4 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array aktualizacji 0.3 informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji Update macierzy wirtualnej StorSimple 0,1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)

