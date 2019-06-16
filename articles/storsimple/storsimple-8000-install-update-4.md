---
title: Instalowanie aktualizacji Update 4 na urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak zainstalować StorSimple 8000 Series Update 4 na swoim urządzeniu serii StorSimple 8000.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860512"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalowanie aktualizacji Update 4 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób instalowania aktualizacji Update 4 na urządzeniu StorSimple ze starszą wersją oprogramowania za pośrednictwem witryny Azure portal i za pomocą metody poprawki. Metoda poprawka jest używana, gdy brama jest skonfigurowana w interfejsie sieciowym innym niż dane 0 urządzenia StorSimple i próbujesz zaktualizować wersję oprogramowania z 1 przed aktualizacją.

4 aktualizacja oprogramowania urządzenia, oprogramowania układowego USM, LSI sterowników i oprogramowania układowego Storport i Spaceport, systemu operacyjnego aktualizacje zabezpieczeń i hostem inne aktualizacje systemu operacyjnego.  Oprogramowanie urządzenia, oprogramowania układowego USM, Spaceport, Storport i inne aktualizacje systemu operacyjnego są niezakłócającego aktualizacje. W witrynie Azure portal lub za pomocą metody poprawkę można zastosować niezakłócającego lub regularne aktualizacje. Aktualizacje oprogramowania układowego dysku aktualizacje wymagają zatrzymania pracy i może być stosowany tylko za pośrednictwem metody poprawek za pomocą interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Zbiór ręczne i automatyczne wstępne kontrole są wykonywane przed instalacji w celu określenia kondycji urządzenia pod kątem sprzętu stanu oraz łączności sieciowej. Te wstępne kontrole są wykonywane tylko wtedy, gdy należy zastosować aktualizacje z witryny Azure portal.
> * Zaleca się zainstalowanie oprogramowania i innych regularne aktualizacje za pośrednictwem witryny Azure portal. Tylko przejdź do interfejsu programu Windows PowerShell urządzenia (w celu instalowania aktualizacji) Jeśli sprawdzenie przed aktualizacją bramy zakończy się niepowodzeniem w portalu. W zależności od wersji aktualizujesz z aktualizacji może potrwać 4 godziny (lub nowszego) do zainstalowania. Należy także zainstalować aktualizacje trybu konserwacji za pośrednictwem interfejsu programu Windows PowerShell urządzenia. Ponieważ aktualizacje trybu konserwacji są aktualizacje wymagają zatrzymania pracy, te spowoduje dół czasu dla danego urządzenia.
> * Jeśli uruchomione opcjonalne programu StorSimple Snapshot Manager, upewnij się, że uaktualniono używanej wersji programu Snapshot Manager do wersji Update 4 przed zaktualizowaniem urządzenia.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalowanie aktualizacji Update 4 w witrynie Azure portal
Wykonaj następujące kroki, aby zaktualizować urządzenie do [aktualizacji Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft ściąga dodatkowych informacji diagnostycznych z urządzenia. Co w efekcie gdy nasz zespół operacyjny identyfikuje urządzeń, które występują problemy, firma Microsoft, są lepiej przystosowany do zbierania informacji z urządzenia i diagnozować problemy. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Sprawdź, czy urządzenie jest uruchomiona **StorSimple 8000 Series Update 4 (6.3.9600.17820)** . **Ostatnia aktualizacja: Data** również powinny być modyfikowane.

* Zobaczysz teraz, że są dostępne aktualizacje trybu konserwacji (ten komunikat może nadal wyświetlane przez maksymalnie 24 godziny po zainstalowaniu aktualizacji). Aktualizacje trybu konserwacji są aktualizacje wymagają zatrzymania pracy skutkować przestojem urządzenia, które mogą być stosowane tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia.

* Pobierz aktualizacje trybu konserwacji, wykonując kroki opisane w [Aby pobrać poprawki](#to-download-hotfixes) do wyszukania i pobrania poprawki KB4011837, który instaluje aktualizacje oprogramowania układowego dysku (inne aktualizacje powinny być już zainstalowane, teraz). Wykonaj kroki opisane w [zainstalować i zweryfikować poprawki przeznaczone do trybu konserwacji](#to-install-and-verify-maintenance-mode-hotfixes) do trybu konserwacji instalacji aktualizacji.

## <a name="install-update-4-as-a-hotfix"></a>Instalowanie aktualizacji Update 4 jako poprawki
Zaleca się instalowanie aktualizacji Update 4 jest za pośrednictwem witryny Azure portal.

Użyj tej procedury, jeśli sprawdzenie bramy po awarii podczas próby zainstalowania aktualizacji w witrynie Azure portal. Sprawdzenie nie powiedzie się, bramy, przypisany do interfejsu sieciowego 0-DATA oraz urządzenia z wersją oprogramowania wcześniejszą niż Update 1.

Dostępne są następujące wersje oprogramowania, które mogą być uaktualnione przy użyciu metody poprawki:

* Update 0.1, 0.2, 0.3
* Aktualizacja 1, 1.1 i 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1


Metoda poprawkę obejmuje następujące trzy kroki:

1. Pobierz poprawki z wykazu usługi Microsoft Update.
2. Zainstalować i zweryfikować poprawki do trybu normalnego.
3. Zainstalować i zweryfikować poprawki trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobierz aktualizacje dla urządzenia

Należy pobrać i zainstalować następujące poprawki w zalecanej kolejności i sugerowane folderów:

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Aktualizacja oprogramowania |Regularne <br></br>Bezproblemowa |~ 25 minut |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Sterownik LSI i aktualizacje oprogramowania układowego <br> Aktualizacja oprogramowania układowego USM (wersja 3.38) |Regularne <br></br>Bezproblemowa |~ 3 godzin <br> (w tym 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobierz system Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobierz system Windows Server 2012 R2 |Regularne <br></br>Bezproblemowa |- |SecondOrderUpdate|

Również może być konieczne zainstalowanie aktualizacji oprogramowania układowego dysku, na podstawie wszystkich aktualizacji, które są wyświetlane w poprzednich tabelach. Możesz sprawdzić, czy potrzebujesz aktualizacje oprogramowania układowego dysku, uruchamiając `Get-HcsFirmwareVersion` polecenia cmdlet. Jeśli używasz tych wersji oprogramowania układowego: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, nie musisz zainstalować te aktualizacje.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji | Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Oprogramowanie układowe dysku |Konserwacja <br></br>Szkodliwe |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Tę procedurę należy wykonać tylko raz do zastosowania aktualizacji w wersji 4. Witryna Azure portal umożliwia stosowanie kolejnych aktualizacji.
> * Jeśli aktualizacja Update 3 lub 3.1, godzina łączna liczba instalacji znajduje się w pobliżu 4 godziny.
> * Przed użyciem tej procedury do zastosowania aktualizacji, upewnij się, że kontrolery urządzeń są w trybie online i wszystkie składniki sprzętowe są w dobrej kondycji.

Wykonaj poniższe kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wersji Update 4](storsimple-update4-release-notes.md).

