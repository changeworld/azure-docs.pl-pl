---
title: Instalowanie aktualizacji Update 5 na urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak zainstalować StorSimple 8000 Series Update 5 na swoim urządzeniu serii StorSimple 8000.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126753"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalowanie aktualizacji Update 5 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób instalowania aktualizacji Update 5 na urządzeniu StorSimple ze starszą wersją oprogramowania za pośrednictwem witryny Azure portal i za pomocą metody poprawki. Metoda poprawka jest używana, podczas próby zainstalowania aktualizacji 5 na urządzeniu z systemem przed aktualizacją 3 wersji. Metoda poprawkę również jest używana, gdy brama jest skonfigurowana w interfejsie sieciowym innym niż dane 0 urządzenia StorSimple i próbujesz zaktualizować wersję oprogramowania z 1 przed aktualizacją.

Aktualizacja 5 obejmuje oprogramowanie urządzenia, Storport i Spaceport, aktualizacje zabezpieczeń systemu operacyjnego i aktualizacje systemu operacyjnego i aktualizacje oprogramowania układowego dysku.  Oprogramowanie urządzenia, Spaceport, Storport, zabezpieczeń i inne aktualizacje systemu operacyjnego są aktualizacje bez zakłóceń. W witrynie Azure portal lub za pomocą metody poprawkę można zastosować niezakłócającego lub regularne aktualizacje. Aktualizacje oprogramowania układowego dysku aktualizacje wymagają zatrzymania pracy i są stosowane, gdy urządzenie jest w trybie konserwacji za pomocą metody poprawek za pomocą interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Aktualizacja 5 jest obowiązkowa aktualizacja i powinny być instalowane natychmiast. Aby uzyskać więcej informacji, zobacz [informacje o wersji z aktualizacją 5](storsimple-update5-release-notes.md).
> * Zbiór ręczne i automatyczne wstępne kontrole są wykonywane przed instalacji w celu określenia kondycji urządzenia pod kątem sprzętu stanu oraz łączności sieciowej. Te wstępne kontrole są wykonywane tylko wtedy, gdy należy zastosować aktualizacje z witryny Azure portal.
> * Zdecydowanie zaleca się, gdy aktualizowane jest urządzenie z systemem wersje przed aktualizacją Update 3, zainstalowanie aktualizacji przy użyciu metody poprawki. Jeśli napotkasz jakiekolwiek problemy [zgłoszenie do pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> * Zaleca się zainstalowanie oprogramowania i innych regularne aktualizacje za pośrednictwem witryny Azure portal. Tylko przejdź do interfejsu programu Windows PowerShell urządzenia (w celu instalowania aktualizacji) Jeśli sprawdzenie przed aktualizacją bramy zakończy się niepowodzeniem w portalu. W zależności od wersji aktualizujesz z aktualizacji może potrwać 4 godziny (lub nowszego) do zainstalowania. Aktualizacje trybu konserwacji musi być zainstalowany za pomocą interfejsu programu Windows PowerShell urządzenia. Ponieważ aktualizacje trybu konserwacji są aktualizacje wymagają zatrzymania pracy, te spowodować dół czasu dla danego urządzenia.
> * Jeśli uruchomione opcjonalne programu StorSimple Snapshot Manager, upewnij się, że przed zaktualizowaniem urządzenia mają uaktualniony używanej wersji programu Snapshot Manager Update 5.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalowanie aktualizacji Update 5 za pomocą witryny Azure portal
Wykonaj następujące kroki, aby zaktualizować urządzenie do [aktualizacji Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft ściąga dodatkowych informacji diagnostycznych z urządzenia. Co w efekcie gdy nasz zespół operacyjny identyfikuje urządzeń, które występują problemy, firma Microsoft, są lepiej przystosowany do zbierania informacji z urządzenia i diagnozować problemy.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Sprawdź, czy urządzenie jest uruchomiona **StorSimple 8000 Series Update 5 (6.3.9600.17845)** . **Ostatnia aktualizacja: Data** powinien być modyfikowany.

Zobaczysz teraz, że są dostępne aktualizacje trybu konserwacji (ten komunikat może nadal wyświetlane przez maksymalnie 24 godziny po zainstalowaniu aktualizacji). Kroki, aby zainstalować aktualizacji w trybie konserwacji są szczegółowo opisane w następnej sekcji.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalowanie aktualizacji Update 5 jako poprawki

Dostępne są następujące wersje oprogramowania, które mogą być uaktualnione przy użyciu metody poprawki:

* Update 0.1, 0.2, 0.3
* Aktualizacja 1, 1.1 i 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4

> [!NOTE] 
> Zaleca się instalowanie aktualizacji Update 5 jest za pośrednictwem witryny Azure portal, podczas próby aktualizacji Update 3 i nowszych wersji. Podczas aktualizowania urządzenia z wersjami przed aktualizacją Update 3, użyj tej procedury. Umożliwia także tę procedurę w przypadku awarii sprawdzenie bramy podczas próby zainstalowania aktualizacji w witrynie Azure portal. Sprawdzenie nie powiedzie się, gdy masz bramy, przypisany do interfejsu sieciowego 0-DATA urządzeniu jest uruchomiona wersja oprogramowania starszych niż Update 1.

Metoda poprawkę obejmuje następujące trzy kroki:

1. Pobierz poprawki z wykazu usługi Microsoft Update.
2. Zainstalować i zweryfikować poprawki do trybu normalnego.
3. Zainstalować i zweryfikować poprawki trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobierz aktualizacje dla urządzenia

Należy pobrać i zainstalować następujące poprawki w zalecanej kolejności i sugerowane folderów:

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizacja oprogramowania<br> Pobierz zarówno _HcsSoftwareUpdate.exe_ i _CisMSDAgent.exe_ |Regularne <br></br>Bezproblemowa |~ 25 minut |FirstOrderUpdate|

Aktualizowania z urządzenia z uruchomioną aktualizacją Update 4, wystarczy zainstalować aktualizacje zbiorcze systemu operacyjnego jako drugi aktualizacje zamówienia.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Pakiet aktualizacji zbiorczej systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|

Jeśli instalowanie z urządzenia z uruchomioną aktualizacją Update 3 lub starszym, należy zainstalować następujące oprócz aktualizacje zbiorcze.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Sterownik LSI i aktualizacje oprogramowania układowego <br> Aktualizacja oprogramowania układowego USM (wersja 3.38) |Regularne <br></br>Bezproblemowa |~ 3 godzin <br> (w tym 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|


Również może być konieczne zainstalowanie aktualizacji oprogramowania układowego dysku, na podstawie wszystkich aktualizacji, które są wyświetlane w poprzednich tabelach. Możesz sprawdzić, czy potrzebujesz aktualizacje oprogramowania układowego dysku, uruchamiając `Get-HcsFirmwareVersion` polecenia cmdlet. Jeśli używasz tych wersji oprogramowania układowego: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, nie musisz zainstalować te aktualizacje.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji | Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Oprogramowanie układowe dysku |Konserwacja <br></br>Szkodliwe |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * W przypadku aktualizacji z aktualizacją Update 4, godzina łączna liczba instalacji znajduje się w pobliżu 4 godziny.
> * Przed użyciem tej procedury do zastosowania aktualizacji, upewnij się, że kontrolery urządzeń są w trybie online i wszystkie składniki sprzętowe są w dobrej kondycji.

Wykonaj poniższe kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wydania aktualizacji Update 5](storsimple-update5-release-notes.md).

