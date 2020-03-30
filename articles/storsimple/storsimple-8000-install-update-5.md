---
title: Zainstaluj aktualizację 5 na urządzeniu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zainstalować aktualizację 5 z serii StorSimple 8000 na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267887"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalowanie aktualizacji 5 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak zainstalować aktualizację 5 na urządzeniu StorSimple z uruchomioną wcześniejszą wersją oprogramowania za pośrednictwem portalu Azure i przy użyciu metody poprawki. Metoda poprawki jest używana podczas próby zainstalowania aktualizacji 5 na urządzeniu z uruchomieniem wersji wstępnej aktualizacji 3. Metoda poprawki jest również używana, gdy brama jest skonfigurowana w interfejsie sieciowym innym niż DATA 0 urządzenia StorSimple i próbujesz zaktualizować z wersji oprogramowania przed aktualizacją 1.

Aktualizacja 5 obejmuje oprogramowanie urządzenia, Storport i Spaceport, aktualizacje zabezpieczeń systemu operacyjnego i aktualizacje systemu operacyjnego oraz aktualizacje oprogramowania układowego dysku.  Oprogramowanie urządzenia, Spaceport, Storport, zabezpieczenia i inne aktualizacje systemu operacyjnego są aktualizacje bez zakłóceń. Aktualizacje nieuzawodne lub regularne mogą być stosowane za pośrednictwem witryny Azure portal lub za pomocą metody poprawki. Aktualizacje oprogramowania układowego dysku są aktualizacje destrukcyjne i są stosowane, gdy urządzenie jest w trybie konserwacji za pomocą metody poprawki przy użyciu interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Aktualizacja 5 jest obowiązkową aktualizacją i powinna zostać zainstalowana natychmiast. Aby uzyskać więcej informacji, zobacz [Aktualizacja 5 informacji o wersji](storsimple-update5-release-notes.md).
> * Przed instalacją przeprowadza się ręczne i automatyczne kontrole wstępne w celu określenia kondycji urządzenia pod względem stanu sprzętu i łączności sieciowej. Te wstępne kontrole są wykonywane tylko wtedy, gdy zastosujesz aktualizacje z witryny Azure portal.
> * Zdecydowanie zaleca się, aby podczas aktualizowania urządzenia z systemem wersji przed aktualizacją 3 aktualizacje były instalowane przy użyciu metody poprawki. Jeśli wystąpią jakiekolwiek problemy, [zarejestruj bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> * Zaleca się zainstalowanie oprogramowania i innych regularnych aktualizacji za pośrednictwem witryny Azure portal. Należy przejść do interfejsu programu Windows PowerShell urządzenia (aby zainstalować aktualizacje), jeśli sprawdzanie bramy przed aktualizacją nie powiedzie się w portalu. W zależności od wersji, z której aktualizujesz, instalacja aktualizacji może potrwać 4 godziny (lub więcej). Aktualizacje trybu konserwacji muszą być zainstalowane za pośrednictwem interfejsu programu Windows PowerShell urządzenia. Ponieważ aktualizacje trybu konserwacji są destrukcyjne aktualizacje, te skutkują przestojem dla urządzenia.
> * Jeśli uruchomiono opcjonalny Menedżer migawek StorSimple, przed zaktualizowaniem urządzenia upewnij się, że uaktualniono wersję Menedżera migawek do aktualizacji 5.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalowanie aktualizacji 5 za pośrednictwem portalu Azure
Wykonaj następujące czynności, aby zaktualizować urządzenie do [aktualizacji 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Firma Microsoft pobiera dodatkowe informacje diagnostyczne z urządzenia. W rezultacie, gdy nasz zespół operacyjny identyfikuje urządzenia, które mają problemy, jesteśmy lepiej przygotowani do zbierania informacji z urządzenia i diagnozowania problemów.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Sprawdź, czy na urządzeniu jest uruchomiona **aktualizacja 5 z serii StorSimple 8000 (6.3.9600.17845)**. Data **ostatniej aktualizacji** powinna zostać zmodyfikowana.

Teraz zobaczysz, że dostępne są aktualizacje trybu konserwacji (ten komunikat może być wyświetlany przez okres do 24 godzin po zainstalowaniu aktualizacji). Kroki, aby zainstalować aktualizację trybu konserwacji są szczegółowo opisane w następnej sekcji.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalowanie aktualizacji 5 jako poprawki

Wersje oprogramowania, które można uaktualnić przy użyciu metody poprawki są:

* Aktualizacja 0.1, 0.2, 0.3
* Aktualizacja 1, 1.1, 1.2
* Aktualizacja 2, 2.1, 2.2
* Aktualizacja 3, 3.1
* Update 4

> [!NOTE] 
> Zalecana metoda instalowania aktualizacji 5 odbywa się za pośrednictwem witryny Azure portal podczas próby aktualizacji z aktualizacji 3 i nowszej wersji. Podczas aktualizowania urządzenia z systemem wersji przed aktualizacją 3, należy użyć tej procedury. Tej procedury można również użyć, jeśli nie powiodą się sprawdzanie bramy podczas próby zainstalowania aktualizacji za pośrednictwem witryny Azure portal. Sprawdzanie kończy się niepowodzeniem, gdy brama jest przypisana do interfejsu sieciowego niezwiązanego z danymi 0, a na urządzeniu jest uruchomiona wersja oprogramowania wcześniejsza niż aktualizacja 1.

Metoda poprawki obejmuje następujące trzy kroki:

1. Pobierz poprawki z wykazu Microsoft Update Catalog.
2. Zainstaluj i sprawdź poprawki trybu regularnego.
3. Zainstaluj i sprawdź poprawkę trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobieranie aktualizacji dla urządzenia

Należy pobrać i zainstalować następujące poprawki w określonej kolejności i sugerowanych folderach:

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Instalacja w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |aktualizacja KB4037264 |Aktualizacja oprogramowania<br> Pobierz zarówno _HcsSoftwareUpdate.exe_ i _CisMSDAgent.exe_ |Zwykłe <br></br>Nieunikliwe |~ 25 min. |FirstOrderUpdate (PierwszyOrderUpdate)|

W przypadku aktualizacji z urządzenia z aktualizacją 4 należy zainstalować tylko aktualizacje zbiorcze systemu operacyjnego jako aktualizacje drugiego zamówienia.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Instalacja w folderze|
| --- | --- | --- | --- | --- | --- |
| 2A. |aktualizacja KB4025336 |Pakiet aktualizacji zbiorczych systemu operacyjnego <br> Pobieranie wersji systemu Windows Server 2012 R2 |Zwykłe <br></br>Nieunikliwe |- |SecondOrderUpdate ( SecondOrderUpdate )|

W przypadku instalacji z urządzenia z aktualizacją 3 lub starszym należy zainstalować następujące elementy oprócz aktualizacji zbiorczych.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Instalacja w folderze|
| --- | --- | --- | --- | --- | --- |
| 2B. |aktualizacja KB4011841 <br> aktualizacja KB4011842 |Aktualizacje sterowników i oprogramowania układowego LSI <br> Aktualizacja oprogramowania układowego USM (wersja 3.38) |Zwykłe <br></br>Nieunikliwe |~ 3 godz. <br> (w tym 2A. + 2B. + 2C.)|SecondOrderUpdate ( SecondOrderUpdate )|
| 2C. |aktualizacja kb3139398 <br> aktualizacja kb3142030 <br> aktualizacja KB3108381 <br> aktualizacja KB3153704 <br> aktualizacja KB3174644 <br> aktualizacja KB3139914   |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobieranie wersji systemu Windows Server 2012 R2 |Zwykłe <br></br>Nieunikliwe |- |SecondOrderUpdate ( SecondOrderUpdate )|
| 2d. |aktualizacja KB3146621 <br> aktualizacja KB3103616 <br> aktualizacja kb3121261 <br> aktualizacja kb3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobieranie wersji systemu Windows Server 2012 R2 |Zwykłe <br></br>Nieunikliwe |- |SecondOrderUpdate ( SecondOrderUpdate )|


Może być również konieczne zainstalowanie aktualizacji oprogramowania układowego dysku na wszystkich aktualizacjach wyświetlanych w poprzednich tabelach. Można sprawdzić, czy potrzebne są aktualizacje `Get-HcsFirmwareVersion` oprogramowania układowego dysku, uruchamiając polecenie cmdlet. Jeśli są uruchomione te wersje `XMGJ` `XGEG`oprogramowania `F6C2` `VR08`układowego: , , `KZ50`, , , `N003` `0107`, to nie trzeba instalować tych aktualizacji.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji | Instalacja w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |aktualizacja KB4037263 |Oprogramowanie układowe dysku |Konserwacja <br></br>Uciążliwy |~ 30 min. | Trzecia(-ś) |

<br></br>

> [!IMPORTANT]
> * W przypadku aktualizacji z aktualizacji 4 całkowity czas instalacji wynosi blisko 4 godziny.
> * Przed użyciem tej procedury, aby zastosować aktualizację, upewnij się, że oba kontrolery urządzeń są w trybie online i wszystkie składniki sprzętu są w dobrej kondycji.

Wykonaj następujące kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wersji aktualizacji 5](storsimple-update5-release-notes.md).

