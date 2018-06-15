---
title: Zainstalować na urządzeniu z serii StorSimple 8000 z aktualizacją Update 4 | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak zainstalować StorSimple 8000 serii aktualizacji 4 na urządzeniu z serii StorSimple 8000.
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
ms.openlocfilehash: 57d6d63c55f8ad4da5d1905a1e209da454b0491c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874934"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalowanie aktualizacji 4 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku przedstawiono sposób instalacji aktualizacji 4 na urządzeniu StorSimple uruchomiona starsza wersja oprogramowania za pośrednictwem portalu Azure i przy użyciu metody poprawki. Metoda poprawki jest używana, gdy brama jest skonfigurowany w interfejsie sieciowym innym niż dane 0 urządzenia StorSimple i są próby aktualizacji z wersji oprogramowania 1 przed aktualizacją.

Aktualizacja 4 zawiera oprogramowania urządzenia, oprogramowania układowego należy LSI sterowników i oprogramowania układowego Storport i Spaceport, systemu operacyjnego aktualizacje zabezpieczeń i inne aktualizacje systemu operacyjnego hosta.  Oprogramowanie urządzenia, należy oprogramowania układowego Spaceport, Storport i inne aktualizacje systemu operacyjnego są Brak aktualizacji. Brak lub regularne aktualizacje mogą być stosowane za pośrednictwem portalu Azure lub za pomocą metody poprawki. Aktualizacje oprogramowania układowego dysku są aktualizacje zakłócenie i mogą być stosowane tylko przy użyciu metody poprawek za pomocą interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Zestaw ręczne i automatyczne wstępne sprawdzanie gotowe czasu zainstalowania w celu określenia kondycji urządzenia pod względem sprzętu stanu i łączność sieciową. Kontrole wstępne są wykonywane tylko wtedy, gdy należy zastosować aktualizacje z portalu Azure.
> * Zaleca się zainstalowanie oprogramowania i innych regularne aktualizacje za pomocą portalu Azure. Tylko powinien możesz przejść do interfejsu programu Windows PowerShell, urządzenia (w celu instalowania aktualizacji), jeśli sprawdzenie przed aktualizacją bramy nie powiedzie się w portalu. W zależności od wersji aktualizujesz z aktualizacji może potrwać 4 godziny (lub nowszego) do zainstalowania. Aktualizacje trybu konserwacji należy także zainstalować za pomocą interfejsu programu Windows PowerShell urządzenia. Aktualizacje trybu konserwacji są aktualizacje destrukcyjne, te spowoduje dół czasu dla danego urządzenia.
> * Uruchomiona opcjonalne StorSimple Snapshot Manager, upewnij się, że uaktualniono wersji Snapshot Manager Update 4 przed zaktualizowaniem urządzenia.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalowanie aktualizacji 4 za pośrednictwem portalu Azure
Wykonaj poniższe kroki, aby zaktualizować urządzenie do [aktualizacja 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft ściąga dodatkowych informacji diagnostycznych z urządzenia. W związku z tym gdy działu operacji identyfikuje urządzenia, które występują problemy, firma Microsoft mają większe możliwości zbierania informacji z urządzenia i diagnozowanie problemów. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Sprawdź, czy urządzenie działa **StorSimple 8000 serii aktualizacji w wersji 4 (6.3.9600.17820)**. **Ostatniej aktualizacji daty** również powinien być modyfikowany.

* Teraz zobaczysz, że są dostępne aktualizacje tryb konserwacji (ten komunikat może nadal wyświetlane przez 24 godziny, po zainstalowaniu aktualizacji). Aktualizacje trybu konserwacji są destrukcyjne aktualizacje, które powoduje przestój urządzenia i mogą być stosowane tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia.

* Pobierz aktualizacje tryb konserwacji przy użyciu kroków opisanych w [do pobrania poprawek](#to-download-hotfixes) do wyszukania i pobrania KB4011837, które instaluje aktualizacje oprogramowania układowego dysku (inne aktualizacje powinny być zainstalowane przez teraz). Wykonaj czynności opisane w [zainstalowany i sprawdź poprawki trybu konserwacji](#to-install-and-verify-maintenance-mode-hotfixes) trybu konserwacji do zainstalowania aktualizacji.

## <a name="install-update-4-as-a-hotfix"></a>Instalowanie aktualizacji 4 jako poprawki
Jest to zalecana metoda instalowania aktualizacji w wersji 4 za pośrednictwem portalu Azure.

Użyj tej procedury, jeśli nie wyboru bramy, podczas próby zainstalowania aktualizacji za pośrednictwem portalu Azure. Sprawdzenie nie powiedzie się, masz przypisane do karty sieciowej 0-DATA bramy i urządzenia z wersją oprogramowania przed Update 1.

Dostępne są następujące wersje oprogramowania, które można uaktualnić za pomocą metody poprawki:

* Zaktualizuj 0,1, 0,2, 0,3
* Aktualizacja 1, 1.1 i 1.2
* Aktualizacja 2, 2.1, 2.2
* Aktualizacja 3 w wersji 3.1


Metoda poprawki obejmuje następujące trzy kroki:

1. Pobierz poprawki z wykazu usługi Microsoft Update.
2. Zainstaluj i sprawdź regularne poprawki.
3. Zainstaluj i sprawdź poprawkę trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobierz aktualizacje dla urządzenia

Należy pobrać i zainstalować poniższe poprawki w określonej kolejności i sugerowane folderów:

| Kolejność | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Aktualizacja oprogramowania |Regularne <br></br>Bezproblemowa |~ 25 minut. |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Sterownik LSI i aktualizacje oprogramowania układowego <br> Aktualizacja oprogramowania układowego należy (wersja 3.38) |Regularne <br></br>Bezproblemowa |~ 3 godziny <br> (dotyczy również 2A. + 2B. + 2 C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobierz system Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobierz system Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|

Należy również zainstalować aktualizacje oprogramowania układowego dysku na wszystkich aktualizacji zamieszczone w poprzednich tabelach. Możesz sprawdzić, czy potrzebujesz aktualizacje oprogramowania układowego dysku, uruchamiając `Get-HcsFirmwareVersion` polecenia cmdlet. Jeśli używasz tych wersji oprogramowania układowego: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, a następnie nie trzeba zainstalować te aktualizacje.

| Kolejność | KB | Opis | Typ aktualizacji | Godzina instalacji | Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Oprogramowanie układowe dysku |Konserwacji <br></br>Problem |~ 30 minut. | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ta procedura musi zostać wykonana tylko raz do zastosowania aktualizacji w wersji 4. Azure portal umożliwia stosowanie kolejnych aktualizacji.
> * Jeśli aktualizacja Update 3 lub 3.1, czas instalacji całkowita jest bliski 4 godziny.
> * Przed użyciem tej procedury, aby zastosować aktualizację, upewnij się, że kontrolery urządzeń są w trybie online i wszystkie składniki sprzętowe są w dobrej kondycji.

Wykonaj poniższe kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wersji aktualizacji 4](storsimple-update4-release-notes.md).

