---
title: Grupami woluminów StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób używania przystawki MMC przystawki StorSimple Snapshot Manager do tworzenia i zarządzanie grupami woluminów.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: e84bc790ac577796e91be010deecc8c5cea1b010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303152"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Tworzenie i zarządzanie grupami woluminów za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Możesz użyć **grupami woluminów** węzeł **zakres** okienko, aby przypisać woluminy do grupy woluminów, Wyświetl informacje o grupie woluminu, zaplanować wykonywanie kopii zapasowych, grupami i edytować wolumin.

Grupy woluminu są pule powiązane woluminy, które umożliwia zagwarantowanie, że kopie zapasowe są spójne z aplikacjami. Aby uzyskać więcej informacji, zobacz [woluminów i grupami woluminów](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) i [integracji z usługą kopiowania woluminów w tle Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Wszystkie woluminy w grupie woluminu musi pochodzić od dostawcy usług w chmurze.
> * Po skonfigurowaniu grupy woluminów, nie należy mieszać udostępnione woluminy klastra (CSV) i innych niż-udostępnione woluminy klastra w tej samej grupie woluminu. Przystawki StorSimple Snapshot Manager nie obsługuje różnych woluminach CSV i innych woluminów CSV w tę samą migawkę.

![Węzeł grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Rysunek 1: Węzeł grup woluminu Menedżera migawek StorSimple** 

W tym samouczku wyjaśniono, jak można użyć przystawki StorSimple Snapshot Manager do:

* Wyświetl informacje o Twoich grup woluminu
* Utwórz grupę woluminu
* Tworzenie kopii zapasowej grupy woluminów
* Edytuj grupę woluminu
* Usuwanie grupy woluminów

Wszystkie te działania są również dostępne na **akcje** okienka.

## <a name="view-volume-groups"></a>Wyświetlanie grup woluminu
Jeśli klikniesz **grupami woluminów** węzła, **wyniki** okienku są wyświetlane następujące informacje dotyczące każdej grupy woluminu, w zależności od wyborów kolumny wprowadzeniu. (W kolumnach **wyniki** okienku są konfigurowane. Kliknij prawym przyciskiem myszy **woluminów** węzeł **widoku**, a następnie wybierz pozycję **Dodaj/Usuń kolumny**.)

| Kolumny wyników | Opis |
|:--- |:--- |
| Name (Nazwa) |**Nazwa** kolumna zawiera nazwę grupy woluminu. |
| Aplikacja |**Aplikacje** kolumnie jest wyświetlana liczba aktualnie zainstalowane składniki zapisywania usługi VSS i uruchomiona na hoście Windows. |
| Wybrano |**Wybrane** kolumnie jest wyświetlana liczba woluminów, które są zawarte w grupie woluminu. Zero (0) oznacza, że żadna aplikacja nie jest skojarzony z woluminami, w grupie woluminu. |
| Zaimportowane |**Zaimportowane** kolumna pokazuje liczbę zaimportowanych woluminów. Po ustawieniu **True**, ta kolumna wskazuje, że grupy woluminów zostały zaimportowane z witryny Azure portal, a nie został utworzony w programie StorSimple Snapshot Manager. |

> [!NOTE]
> Grupami woluminów StorSimple Snapshot Manager są również wyświetlane na **zasady tworzenia kopii zapasowych** kartę w witrynie Azure portal.
> 
> 

## <a name="create-a-volume-group"></a>Utwórz grupę woluminu
Poniższa procedura umożliwia utworzenie grupy woluminu.

#### <a name="to-create-a-volume-group"></a>Aby utworzyć grupę woluminu
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **grupami woluminów**, a następnie kliknij przycisk **Utwórz grupę woluminu**.
   
    ![Tworzenie grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    **Utwórz grupę woluminu** pojawi się okno dialogowe.
   
    ![Tworzenie okna dialogowego grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Wprowadź następujące informacje:
   
   1. W **nazwa** wpisz unikatową nazwę dla nowej grupy woluminu.
   2. W **aplikacje** polu Wybierz aplikacji skojarzonych z woluminów, które ma być dodany do grupy woluminu.
      
       **Aplikacje** listy pola te aplikacje, które korzysta z woluminów StorSimple i mają składniki zapisywania usługi VSS są włączone dla nich. Składnik zapisywania usługi VSS jest włączona tylko wtedy, gdy wszystkie woluminy, które moduł zapisujący jest świadomy woluminów StorSimple. Jeśli okno aplikacji jest pusta, żadnych aplikacji, które korzysta z woluminów StorSimple systemu Azure i mają obsługiwane składniki zapisywania usługi VSS są instalowane. (Obecnie usługa Azure StorSimple obsługuje program Microsoft Exchange i SQL Server). Aby uzyskać więcej informacji na temat składniki zapisywania usługi VSS, zobacz [integracji z usługą kopiowania woluminów w tle Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Jeśli wybierzesz aplikację, wszystkich woluminów skojarzonych z nim zostaną zaznaczone automatycznie. Z drugiej strony, jeśli wybierzesz woluminów skojarzonych z określoną aplikacją, aplikacja jest automatycznie wybierany w **aplikacje** pole. 
   3. W **woluminów** wybierz woluminy StorSimple można dodać do grupy woluminu. 
      
      * Może zawierać woluminy z jednego lub wielu partycjach. (Wiele woluminów partycji może być dynamicznych dysków lub dysków podstawowych z wielu partycji.) Wolumin, który zawiera wiele partycji jest traktowany jako pojedyncza jednostka. W związku z tym jeśli dodasz tylko jednej z partycji do grupy woluminów, wszystkie pozostałe partycje są automatycznie dodawane do tej grupy woluminu w tym samym czasie. Po dodaniu wielu woluminu partycji do grupy woluminu wiele woluminu partycji w dalszym ciągu być traktowane jako pojedyncza jednostka.
      * Aby utworzyć pusty wolumin grupy, należy nie przypisywanie woluminów do nich. 
      * Nie należy mieszać udostępnione woluminy klastra (CSV) i innych niż-udostępnione woluminy klastra w tej samej grupie woluminu. Przystawki StorSimple Snapshot Manager nie obsługuje różnych woluminów CSV i woluminy niebędący woluminem CSV w tę samą migawkę.
4. Kliknij przycisk **OK** można zapisać grupy woluminu.

## <a name="back-up-a-volume-group"></a>Tworzenie kopii zapasowej grupy woluminów
Użyj poniższej procedury do utworzenia kopii zapasowej grupy woluminu.

#### <a name="to-back-up-a-volume-group"></a>Aby utworzyć kopię zapasową grupy woluminów
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **grupami woluminów** węzła, kliknij prawym przyciskiem myszy nazwę grupy woluminu, a następnie kliknij przycisk **wykonać kopię zapasową**.
   
    ![Natychmiastowe tworzenie kopii zapasowej grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. W **wykonać kopię zapasową** okno dialogowe, wybierz opcję **migawka lokalna** lub **migawki w chmurze**, a następnie kliknij przycisk **Utwórz**.
   
    ![Wykonaj okna dialogowego kopii zapasowej](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Aby upewnić się, że kopia zapasowa jest uruchomiona, należy rozwinąć **zadania** węzłem, a następnie kliknij przycisk **systemem**. Tworzenie kopii zapasowej powinny zostać wyświetlone.
5. Aby wyświetlić ukończone migawki, rozwiń węzeł **katalog kopii zapasowej** węzła, rozwiń węzeł z nazwą grupy woluminów, a następnie kliknij **migawka lokalna** lub **migawki w chmurze**. Tworzenie kopii zapasowej zostaną wyświetlone, jeśli zostało zakończone pomyślnie.

## <a name="edit-a-volume-group"></a>Edytuj grupę woluminu
Poniższa procedura umożliwia edytowanie grupy woluminu.

#### <a name="to-edit-a-volume-group"></a>Aby edytować grupę woluminu
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **grupami woluminów** węzła, kliknij prawym przyciskiem myszy nazwę grupy woluminu, a następnie kliknij przycisk **Edytuj**.
3. ** Tworzenie grupy woluminu ** pojawi się okno dialogowe. Możesz zmienić **nazwa**, **aplikacje**, i **woluminów** wpisów.
4. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="delete-a-volume-group"></a>Usuwanie grupy woluminów
Użyj poniższej procedury można usunąć grupy woluminu. 

> [!WARNING]
> Spowoduje to również usunięcie wszystkich kopii zapasowych skojarzone z grupą woluminu.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Aby usunąć grupę woluminu
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **grupami woluminów** węzła, kliknij prawym przyciskiem myszy nazwę grupy woluminu, a następnie kliknij przycisk **Usuń**.
3. **Usuń grupę woluminu** pojawi się okno dialogowe. Typ **Potwierdź** w polu tekstowym, a następnie kliknij przycisk **OK**.
   
    Grupa usunięto wolumin znika z listy w **wyniki** okienka i wszystkie kopie zapasowe, które są skojarzone z tą grupą woluminu są usuwane z wykazem kopii zapasowych.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [programu StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie zasadami tworzenia kopii zapasowych](storsimple-snapshot-manager-manage-backup-policies.md).

