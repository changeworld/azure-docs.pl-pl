---
title: Instalowanie aktualizacji 5 na urządzeniu z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak zainstalować StorSimple 8000 serii aktualizacji 5 na urządzeniu z serii StorSimple 8000.
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
ms.openlocfilehash: d6e17c7609fd41b8f4457edda373f6882a1a9d2b
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2018
ms.locfileid: "28108724"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalowanie aktualizacji 5 na urządzeniu StorSimple

## <a name="overview"></a>Przegląd

W tym samouczku przedstawiono sposób instalacji aktualizacji 5 na urządzeniu StorSimple uruchomiona starsza wersja oprogramowania za pośrednictwem portalu Azure i przy użyciu metody poprawki. Metoda poprawki jest używany podczas próby zainstalowania aktualizacji 5 na urządzeniu z systemem w wersji 3 przed aktualizacją. Metoda poprawki również jest używana, gdy brama jest skonfigurowany w interfejsie sieciowym innym niż dane 0 urządzenia StorSimple i są próby aktualizacji z wersji oprogramowania 1 przed aktualizacją.

Aktualizacja 5 obejmuje oprogramowanie urządzenia, Storport i Spaceport, aktualizacje zabezpieczeń systemu operacyjnego i aktualizacje systemu operacyjnego, a aktualizacje oprogramowania układowego dysku.  Oprogramowanie urządzenia, Spaceport Storport, zabezpieczeń i inne aktualizacje systemu operacyjnego są Brak aktualizacji. Brak lub regularne aktualizacje mogą być stosowane za pośrednictwem portalu Azure lub za pomocą metody poprawki. Aktualizacje oprogramowania układowego dysku destrukcyjne aktualizacji i są stosowane, gdy urządzenie jest w trybie konserwacji za pomocą metody poprawek za pomocą interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Aktualizacja 5 jest to aktualizacja wymagana i powinny być instalowane natychmiast. Aby uzyskać więcej informacji, zobacz [wersji aktualizacji 5](storsimple-update5-release-notes.md).
> * Zestaw ręczne i automatyczne wstępne sprawdzanie gotowe czasu zainstalowania w celu określenia kondycji urządzenia pod względem sprzętu stanu i łączność sieciową. Kontrole wstępne są wykonywane tylko wtedy, gdy należy zastosować aktualizacje z portalu Azure.
> * Zdecydowanie zaleca się, że podczas aktualizowania urządzenia z wersjami przed Update 3, zainstalować aktualizacje za pomocą metody poprawki. Jeśli wystąpią problemy, [dziennika biletu pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> * Zaleca się zainstalowanie oprogramowania i innych regularne aktualizacje za pomocą portalu Azure. Tylko powinien możesz przejść do interfejsu programu Windows PowerShell, urządzenia (w celu instalowania aktualizacji), jeśli sprawdzenie przed aktualizacją bramy nie powiedzie się w portalu. W zależności od wersji aktualizujesz z aktualizacji może potrwać 4 godziny (lub nowszego) do zainstalowania. Aktualizacje trybu konserwacji musi być zainstalowany za pośrednictwem interfejsu programu Windows PowerShell urządzenia. Aktualizacje trybu konserwacji są destrukcyjne aktualizacje, te spowodować dół czasu dla danego urządzenia.
> * Uruchomiona opcjonalne StorSimple Snapshot Manager, upewnij się, że wersji Snapshot Manager zostały uaktualnione do aktualizacji 5 przed zaktualizowaniem urządzenia.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalowanie aktualizacji 5 za pomocą portalu Azure
Wykonaj poniższe kroki, aby zaktualizować urządzenie do [aktualizacji 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft ściąga dodatkowych informacji diagnostycznych z urządzenia. W związku z tym gdy działu operacji identyfikuje urządzenia, które występują problemy, firma Microsoft mają większe możliwości zbierania informacji z urządzenia i diagnozowanie problemów.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Sprawdź, czy urządzenie działa **StorSimple 8000 serii aktualizacji 5 (6.3.9600.17845)**. **Ostatniej aktualizacji daty** powinien być modyfikowany.

Teraz zobaczysz, że są dostępne aktualizacje tryb konserwacji (ten komunikat może nadal wyświetlane przez 24 godziny, po zainstalowaniu aktualizacji). Kroki, aby zainstalować aktualizację trybu konserwacji są szczegółowo opisane w następnej sekcji.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalowanie aktualizacji 5 jako poprawki

Dostępne są następujące wersje oprogramowania, które można uaktualnić za pomocą metody poprawki:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Aktualizacja 4

> [!NOTE] 
> Do zainstalowania aktualizacji 5 zaleca za pośrednictwem portalu Azure podczas próby aktualizacji Update 3 i nowszych wersji. Podczas aktualizowania urządzenia z wersjami przed Update 3, użyj tej procedury. Można również Użyj tej procedury, jeśli nie wyboru bramy, podczas próby zainstalowania aktualizacji za pośrednictwem portalu Azure. Sprawdzenie nie powiedzie się, gdy brama przypisany do interfejsu sieciowego 0-DATA urządzeniu jest uruchomiona wersja oprogramowania starszych niż Update 1.

Metoda poprawki obejmuje następujące trzy kroki:

1. Pobierz poprawki z wykazu usługi Microsoft Update.
2. Zainstaluj i sprawdź regularne poprawki.
3. Zainstaluj i sprawdź poprawkę trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobierz aktualizacje dla urządzenia

Należy pobrać i zainstalować poniższe poprawki w określonej kolejności i sugerowane folderów:

| Kolejność | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizacja oprogramowania<br> Pobierz oba _HcsSoftwareUpdate.exe_ i _CisMSDAgent.exe_ |Regularne <br></br>Bezproblemowa |~ 25 mins |FirstOrderUpdate|

Aktualizowania z urządzenia z uruchomioną aktualizacji 4, wystarczy zainstalować aktualizacje zbiorcze systemu operacyjnego jako drugi aktualizacje kolejności.

| Kolejność | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Pakiet aktualizacji zbiorczych systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|

W przypadku instalowania z urządzenia z uruchomioną aktualizacji 3 lub starszym, należy zainstalować następujące oprócz aktualizacje zbiorcze.

| Kolejność | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Sterownik LSI i aktualizacje oprogramowania układowego <br> Aktualizacja oprogramowania układowego należy (wersja 3.38) |Regularne <br></br>Bezproblemowa |~ 3 godziny <br> (dotyczy również 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|


Należy również zainstalować aktualizacje oprogramowania układowego dysku na wszystkich aktualizacji zamieszczone w poprzednich tabelach. Możesz sprawdzić, czy potrzebujesz aktualizacje oprogramowania układowego dysku, uruchamiając `Get-HcsFirmwareVersion` polecenia cmdlet. Jeśli używasz tych wersji oprogramowania układowego: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, a następnie nie trzeba zainstalować te aktualizacje.

| Kolejność | KB | Opis | Typ aktualizacji | Godzina instalacji | Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Oprogramowanie układowe dysku |Konserwacja <br></br>Problem |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Aktualizowania z aktualizacją Update 4, czas instalacji całkowita jest bliski 4 godziny.
> * Przed użyciem tej procedury, aby zastosować aktualizację, upewnij się, że kontrolery urządzeń są w trybie online i wszystkie składniki sprzętowe są w dobrej kondycji.

Wykonaj poniższe kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wersji aktualizacji 5](storsimple-update5-release-notes.md).

