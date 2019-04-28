---
title: StorSimple wirtualnego tablicy Update 0.6 informacje o wersji | Dokumentacja firmy Microsoft
description: Opis macierzy wirtualnej StorSimple z aktualizacją Update 0.6 krytyczne nierozwiązane problemy i rozwiązania.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: e984531feced2d61332e4c399848c12cd245a34a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870710"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple wirtualnego tablicy Update 0.6 informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem rozwiązania StorSimple Virtual Array, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Update 0.6 odnosi się do wersji oprogramowania **10.0.10293.0**.

> [!IMPORTANT]
> - Aktualizacje są naturalnymi i ponownym uruchomieniu urządzenia. W przypadku operacji We/Wy w toku, urządzenie wiąże się z przestojem. Aby uzyskać szczegółowe instrukcje dotyczące sposobu stosowania aktualizacji, przejdź do [instalowanie aktualizacji Update 0.6](storsimple-virtual-array-install-update-06.md).
>
> - Zdecydowanie zaleca się instalowanie aktualizacji Update 0.6 natychmiast, ponieważ zawiera on krytycznych poprawek.


## <a name="whats-new-in-the-update-06"></a>Co nowego w aktualizacji Update 0.6
Update 0.6 jest krytycznych aktualizacji i powinny być wdrażane bezpośrednio. Ta aktualizacja zawiera następujące poprawki: 

- **Poprawki zabezpieczeń Windows** — ta wersja ma **Windows krytycznych poprawek**. Przejrzyj następujące aktualizacje zabezpieczeń, aby uzyskać więcej informacji na temat problemów z zabezpieczeniami i skojarzone poprawki:
    - [Grudnia 2016 jakością jedynym zabezpieczeń aktualizacji dla Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Zabezpieczenia jakości tylko aktualizacja z marca 2017 for Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 maja 2017 r. — KB4019213 (aktualizacja tylko do zabezpieczeń)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Przywróć poprawkę** — we wcześniejszych wersjach, zostało usterki, które mogłyby uniemożliwiać przywracania ukończenie. Ten błąd został naprawiony w tej wersji.


## <a name="issues-fixed-in-the-update-06"></a>Problemy rozwiązane w aktualizacji Update 0.6

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Bezpieczeństwo| Ta wersja zawiera krytyczne aktualizacje zabezpieczeń Windows. Zalecamy natychmiastowe zainstalowanie tej aktualizacji.|
| 2 |Przywracanie| Podczas przywracania wystąpił wyścigu, które mogłyby uniemożliwiać zadania przywracania na ukończenie. Naprawienie usterki dotyczy sytuacja wyścigu.|


## <a name="known-issues-in-the-update-06"></a>Znane problemy w aktualizacji Update 0.6

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
| **17.** |Aktualizacje |Jeśli zostanie wyświetlony błąd kodu: 2359302 (szesnastkowo 0x240006) podczas próby zainstalowania poprawki przy użyciu lokalnego interfejsu użytkownika, następnie oznacza to, że już zainstalowana na urządzeniu.   | |

## <a name="next-step"></a>Następny krok
[Instalowanie aktualizacji Update 0.6](storsimple-virtual-array-install-update-06.md) na rozwiązania StorSimple Virtual Array.

## <a name="references"></a>Dokumentacja
Szukasz starszej wersji? Przejdź do strony:

* [Informacje o wersji 0,5 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Informacje o wersji 0,4 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array aktualizacji 0.3 informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji Update macierzy wirtualnej StorSimple 0,1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)

