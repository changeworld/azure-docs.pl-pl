---
title: Zainstaluj aktualizację 1.0 w macierzy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania interfejsu użytkownika sieci web tablicy wirtualnego StorSimple do stosowania aktualizacji za pomocą metody Azure portal i poprawki
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
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012078"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Zainstaluj aktualizację 1.0 na tablica wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 1.0 na tablica wirtualnego StorSimple za pośrednictwem lokalnego interfejsu użytkownika sieci web i portalu Azure.

Należy zastosować aktualizacje oprogramowania lub poprawek, aby zapewnić aktualność tablica wirtualne StorSimple. Przed zainstalowaniem aktualizacji zalecamy wykonanie woluminy lub udziały w trybie offline na hoście pierwszy, a następnie urządzenia. Pozwala to zmniejszyć możliwości uszkodzenie danych. Po woluminy lub udziały są w trybie offline, należy również wziąć ręcznego tworzenia kopii zapasowych urządzenia.

> [!IMPORTANT]
> - Update 1.0 odpowiada **10.0.10296.0** wersji oprogramowania na urządzeniu. Aby uzyskać informacji na temat nowości w ramach tej aktualizacji, przejdź do [informacje o wersji 1.0 aktualizacji](storsimple-virtual-array-update-1-release-notes.md).
>
> - Należy pamiętać, polegające na zainstalowanie aktualizacji lub poprawki ponownym uruchomieniu urządzenia. Biorąc pod uwagę, że tablica wirtualne StorSimple jest urządzeniem jeden węzeł, wszystkie operacje We/Wy w toku jest zakłócona i urządzenia napotyka przestoju.
>
> - Aktualizacja 1 jest dostępna w portalu Azure, tylko wtedy, gdy wirtualne tablicy działa aktualizacji 0,6. Dla wirtualnej tablic z wersjami 0,6 przed aktualizacją należy najpierw zainstalować aktualizację 0,6, a następnie zainstaluj Update 1.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Z aktualizacji, 0,2 i nowszymi wersjami, zaleca się zainstalowanie aktualizacji za pośrednictwem portalu Azure. Procedury portalu wymaga od użytkownika skanowania, Pobierz i zainstaluj aktualizacje. W zależności od wersji oprogramowania, na którym uruchomiona jest tablica wirtualnego stosowania aktualizacji za pośrednictwem portalu Azure jest inna.

 - Jeśli tablica wirtualnego działa aktualizacji 0,6, portalu Azure bezpośrednio instaluje Update 1 (10.0.10296.0) na urządzeniu. Ta procedura trwa około 7 minut do wykonania.
 - Jeśli tablica wirtualnego jest zainstalowana wersja przed aktualizacją 0,6, aktualizacji odbywa się na etapie dwa. Azure portal najpierw instaluje aktualizację 0,6 (10.0.10293.0) na urządzeniu. Uruchamia wirtualnego tablicy, a następnie instaluje Update 1 (10.0.10296.0) na urządzeniu w portalu. Ta procedura trwa około 15 minut do ukończenia.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po zakończeniu instalacji, przejdź do usługi Menedżer StorSimple urządzenia. Wybierz **urządzeń** , a następnie wybierz i kliknij przycisk po zaktualizowaniu urządzenia. Przejdź do **Ustawienia > Zarządzaj > aktualizacji urządzenia**. Wersja oprogramowania wyświetlane **10.0.10296.0**.

![Wersja oprogramowania po aktualizacji](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Użyj lokalnego interfejsu użytkownika sieci web

Istnieją dwa kroki, korzystając z lokalnego interfejsu użytkownika sieci web:

* Pobieranie aktualizacji lub poprawek
* Zainstalowanie aktualizacji lub poprawek

> [!IMPORTANT] 
> **Ta aktualizacja należy kontynuować tylko wtedy, gdy używasz wersji Update 0,6 (10.0.10293.0). Jeśli używasz starszej wersji, [instalacji aktualizacji 0,6](storsimple-virtual-array-install-update-06.md) na twoim urządzeniu pierwszy, a następnie zastosować Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Pobieranie aktualizacji lub poprawek

Jeśli tablica wirtualnego działa aktualizacji 0,6, wykonaj następujące czynności na pobieranie aktualizacji 1 z wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizacji lub poprawek

1. Uruchom program Internet Explorer i przejdź pod adres [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Jeśli korzystasz z wykazu usługi Microsoft Update po raz pierwszy na tym komputerze, kliknij przycisk **zainstalować** monit o zainstalowanie dodatku wykazu usługi Microsoft Update.

3. W polu wyszukiwania z wykazu usługi Microsoft Update wprowadź numer bazy wiedzy Knowledge Base (KB) poprawki, którą chcesz pobrać. Wprowadź **4047203** dla aktualizacji wersji 1.0, a następnie kliknij przycisk **wyszukiwania**.
   
    Poprawka zostanie wyświetlona na liście, na przykład **StorSimple wirtualnego tablicy Update 1.0**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kliknij pozycję **Pobierz**.

5. Pobierz oba pliki do folderu. Można także skopiować folder do udziału sieciowego, który jest dostępny z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.

    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zostaną wyświetlone dwa pliki:
    -  Plik pakietu autonomicznego aktualizacji firmy Microsoft `WindowsTH-KB3011067-x64`. Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik zawierający aktualizacji zbiorczych dla sierpnia `windows8.1-kb4034681-x64`. Aby uzyskać więcej informacji o dostępnych w tym pakiecie zbiorczym, przejdź do [sierpnia miesięczne zbiorczy zabezpieczeń](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Zainstalowanie aktualizacji lub poprawek

Przed instalacją aktualizacji lub poprawki upewnij się, że:

 - Masz aktualizacja lub poprawka pobierane lokalnie na hoście lub dostępny za pośrednictwem udziału sieciowego.
 - Tablica wirtualnego działa aktualizacji 0,6 (10.0.10293.0). Jeśli używasz wersji przed aktualizacją 0,6, [instalacji aktualizacji 0,6](storsimple-virtual-array-install-update-06.md) pierwszy, a następnie zainstaluj aktualizację 1.

Ta procedura trwa około 4 minut do wykonania. Wykonaj poniższe kroki, aby zainstalować aktualizacja lub poprawka.

#### <a name="to-install-the-update-or-the-hotfix"></a>Do zainstalowania aktualizacji lub poprawek

1. W lokalnej sieci web interfejsu użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Zanotuj wersji oprogramowania, na którym są uruchomione. **Ta aktualizacja należy kontynuować tylko wtedy, gdy używasz wersji Update 0,6 (10.0.10293.0). Jeśli używasz starszej wersji, [instalacji aktualizacji 0,6](storsimple-virtual-array-install-update-06.md) na twoim urządzeniu pierwszy, a następnie zastosować Update 1.**
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. W **ścieżka pliku aktualizacji**, wprowadź nazwę pliku dla aktualizacji lub poprawek. Możesz również przejść do lokalizacji pliku instalacyjnego aktualizacja lub poprawka umieszczony w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Podana tablica wirtualnego jest urządzeniem jednego węzła, po zastosowaniu aktualizacji, ponownym uruchomieniu urządzenia i brak przestojów. Kliknij ikonę znacznika wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Aktualizacja zostanie uruchomiony. Po pomyślnym zaktualizowaniu urządzenia ponownego uruchomienia. Lokalnego interfejsu użytkownika nie jest dostępny w tym przedziale czasu.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po ponownym uruchomieniu komputera, zostają przeniesieni do **Zaloguj** strony. Aby zweryfikować, że oprogramowanie urządzenia został zaktualizowany w lokalnej sieci web interfejsu użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Wersja oprogramowania wyświetlane **10.0.0.0.0.10296** Update 1.0.
   
   > [!NOTE]
   > Wersje oprogramowania Microsoft raport w sposób nieco inne w lokalnym interfejsu użytkownika sieci web i portalu Azure. Na przykład raporty lokalnego interfejsu użytkownika sieci web **10.0.0.0.0.10296** i raporty portalu Azure **10.0.10296.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Powtórz kroki 2-4, aby zainstalować poprawkę zabezpieczeń systemu Windows przy użyciu pliku `windows8.1-kb4012213-x64`. Wirtualnego tablicy ponownych uruchomień po zakończeniu instalacji, należy zalogować się do lokalnego interfejsu użytkownika sieci web.

> [!NOTE]
> Bezpośrednio po zastosowaniu aktualizacji 1 na urządzeniu z systemem w wersji przed aktualizacją 0,6, że brakuje niektórych aktualizacji. Dalsze czynności, skontaktuj się z Microsoft Support.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowanie tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).
