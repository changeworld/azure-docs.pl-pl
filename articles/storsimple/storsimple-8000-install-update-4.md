---
title: Zainstaluj aktualizację 4 na urządzeniu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zainstalować aktualizację 4 z serii StorSimple 8000 na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60860512"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalowanie aktualizacji 4 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak zainstalować aktualizację 4 na urządzeniu StorSimple z uruchomioną wcześniejszą wersją oprogramowania za pośrednictwem portalu Azure i przy użyciu metody poprawki. Metoda poprawki jest używana, gdy brama jest skonfigurowana w interfejsie sieciowym innym niż DATA 0 urządzenia StorSimple i próbujesz zaktualizować z wersji oprogramowania przed aktualizacją 1.

Aktualizacja 4 obejmuje oprogramowanie urządzenia, oprogramowanie układowe USM, sterownik LSI i oprogramowanie układowe, Storport i Spaceport, aktualizacje zabezpieczeń systemu operacyjnego i wiele innych aktualizacji systemu operacyjnego.  Oprogramowanie urządzenia, oprogramowanie układowe USM, spaceport, Storport i inne aktualizacje systemu operacyjnego są aktualizacje nieoczyszające. Aktualizacje nieuzawodne lub regularne mogą być stosowane za pośrednictwem witryny Azure portal lub za pomocą metody poprawki. Aktualizacje oprogramowania układowego dysku są aktualizacje destrukcyjne i mogą być stosowane tylko za pomocą metody poprawki przy użyciu interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Przed instalacją przeprowadza się ręczne i automatyczne kontrole wstępne w celu określenia kondycji urządzenia pod względem stanu sprzętu i łączności sieciowej. Te wstępne kontrole są wykonywane tylko wtedy, gdy zastosujesz aktualizacje z witryny Azure portal.
> * Zaleca się zainstalowanie oprogramowania i innych regularnych aktualizacji za pośrednictwem witryny Azure portal. Należy przejść do interfejsu programu Windows PowerShell urządzenia (aby zainstalować aktualizacje), jeśli sprawdzanie bramy przed aktualizacją nie powiedzie się w portalu. W zależności od wersji, z której aktualizujesz, instalacja aktualizacji może potrwać 4 godziny (lub więcej). Aktualizacje trybu konserwacji muszą być również zainstalowane za pośrednictwem interfejsu programu Windows PowerShell urządzenia. Ponieważ aktualizacje trybu konserwacji są destrukcyjne aktualizacje, spowoduje to czas przestoju dla urządzenia.
> * Jeśli uruchomiono opcjonalny Menedżer migawek StorSimple, przed zaktualizowaniem urządzenia upewnij się, że uaktualniono wersję Menedżera migawek do aktualizacji 4.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalowanie aktualizacji 4 za pośrednictwem portalu Azure
Wykonaj następujące czynności, aby zaktualizować urządzenie do [aktualizacji 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Firma Microsoft pobiera dodatkowe informacje diagnostyczne z urządzenia. W rezultacie, gdy nasz zespół operacyjny identyfikuje urządzenia, które mają problemy, jesteśmy lepiej przygotowani do zbierania informacji z urządzenia i diagnozowania problemów. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Sprawdź, czy na urządzeniu jest uruchomiona **aktualizacja 4 z serii StorSimple 8000 (6.3.9600.17820)**. Data **ostatniej aktualizacji** również powinna zostać zmodyfikowana.

* Teraz zobaczysz, że dostępne są aktualizacje trybu konserwacji (ten komunikat może być wyświetlany przez okres do 24 godzin po zainstalowaniu aktualizacji). Aktualizacje trybu konserwacji są przełomowe aktualizacje, które powodują przestoje urządzenia i mogą być stosowane tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia.

* Pobierz aktualizacje trybu konserwacji, wykonując czynności wymienione w [celu pobrania poprawek](#to-download-hotfixes) w celu wyszukania i pobrania aktualizacji KB4011837, która instaluje aktualizacje oprogramowania układowego dysku (pozostałe aktualizacje powinny być już zainstalowane). Wykonaj kroki wymienione w [install i sprawdź poprawki trybu konserwacji,](#to-install-and-verify-maintenance-mode-hotfixes) aby zainstalować aktualizacje trybu konserwacji.

## <a name="install-update-4-as-a-hotfix"></a>Instalowanie aktualizacji 4 jako poprawki
Zalecana metoda instalowania aktualizacji 4 odbywa się za pośrednictwem witryny Azure portal.

Tej procedury należy użyć, jeśli nie powiodą się sprawdzanie bramy podczas próby zainstalowania aktualizacji za pośrednictwem witryny Azure portal. Sprawdzanie kończy się niepowodzeniem, ponieważ brama jest przypisana do interfejsu sieciowego niezwiązanego z DANYMI 0, a na urządzeniu jest uruchomiona wersja oprogramowania przed aktualizacją 1.

Wersje oprogramowania, które można uaktualnić przy użyciu metody poprawki są:

* Aktualizacja 0.1, 0.2, 0.3
* Aktualizacja 1, 1.1, 1.2
* Aktualizacja 2, 2.1, 2.2
* Aktualizacja 3, 3.1


Metoda poprawki obejmuje następujące trzy kroki:

1. Pobierz poprawki z wykazu Microsoft Update Catalog.
2. Zainstaluj i sprawdź poprawki trybu regularnego.
3. Zainstaluj i sprawdź poprawkę trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobieranie aktualizacji dla urządzenia

Należy pobrać i zainstalować następujące poprawki w określonej kolejności i sugerowanych folderach:

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Instalacja w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |aktualizacja KB4011839 |Aktualizacja oprogramowania |Zwykłe <br></br>Nieunikliwe |~ 25 min. |FirstOrderUpdate (PierwszyOrderUpdate)|
| 2A. |aktualizacja KB4011841 <br> aktualizacja KB4011842 |Aktualizacje sterowników i oprogramowania układowego LSI <br> Aktualizacja oprogramowania układowego USM (wersja 3.38) |Zwykłe <br></br>Nieunikliwe |~ 3 godz. <br> (w tym 2A. + 2B. + 2C.)|SecondOrderUpdate ( SecondOrderUpdate )|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobieranie systemu Windows Server 2012 R2 |Zwykłe <br></br>Nieunikliwe |- |SecondOrderUpdate ( SecondOrderUpdate )|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> aktualizacja kb3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobieranie systemu Windows Server 2012 R2 |Zwykłe <br></br>Nieunikliwe |- |SecondOrderUpdate ( SecondOrderUpdate )|

Może być również konieczne zainstalowanie aktualizacji oprogramowania układowego dysku na wszystkich aktualizacjach wyświetlanych w poprzednich tabelach. Można sprawdzić, czy potrzebne są aktualizacje `Get-HcsFirmwareVersion` oprogramowania układowego dysku, uruchamiając polecenie cmdlet. Jeśli są uruchomione te wersje `XMGJ` `XGEG`oprogramowania `F6C2` `VR08`układowego: , , `KZ50`, , , `N002` `0106`, to nie trzeba instalować tych aktualizacji.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji | Instalacja w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |aktualizacja KB3121899 |Oprogramowanie układowe dysku |Konserwacja <br></br>Uciążliwy |~ 30 min. | Trzecia(-ś) |

<br></br>

> [!IMPORTANT]
> * Tę procedurę należy wykonać tylko raz, aby zastosować aktualizację 4. Za pomocą witryny Azure portal można zastosować kolejne aktualizacje.
> * W przypadku aktualizacji z aktualizacji 3 lub 3.1 całkowity czas instalacji wynosi blisko 4 godziny.
> * Przed użyciem tej procedury, aby zastosować aktualizację, upewnij się, że oba kontrolery urządzeń są w trybie online i wszystkie składniki sprzętu są w dobrej kondycji.

Wykonaj następujące kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wersji aktualizacji 4](storsimple-update4-release-notes.md).

