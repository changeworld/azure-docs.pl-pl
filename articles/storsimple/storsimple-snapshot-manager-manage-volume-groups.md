---
title: Grupy woluminu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania przystawki MMC programu StorSimple Snapshot Manager do tworzenia i zarządzania grupami woluminu.
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
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie grupami woluminu
## <a name="overview"></a>Przegląd
Można użyć **grup woluminu** węzła na **zakres** okienko, aby przypisać woluminy do grup woluminu, Wyświetl informacje o grupie woluminu, zaplanować wykonywanie kopii zapasowych i edytować grupy woluminu.

Wolumin grupy są pule powiązanych woluminów używanych do zapewnienia, że kopie zapasowe są spójne z aplikacjami. Aby uzyskać więcej informacji, zobacz [woluminów i woluminów grupy](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) i [integracji z usługą kopiowania woluminów w tle Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Wszystkie woluminy w grupie woluminu musi pochodzić od dostawcy usług w chmurze pojedynczego.
> * Po skonfigurowaniu grup woluminu niemieszanie udostępnione woluminy klastra (CSV), a nie CSV w tej samej grupie woluminu. StorSimple Snapshot Manager nie obsługuje zarówno udostępnionych woluminów klastra i z systemem innym niż udostępnionych woluminów klastra w tej samej migawki.

![Wolumin węzeł grupy](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Rysunek 1: Węzeł StorSimple Snapshot Manager woluminu grup** 

W tym samouczku wyjaśniono, jak używasz StorSimple Snapshot Manager do:

* Wyświetlanie informacji o woluminie grup
* Utwórz grupę woluminu
* Tworzenie kopii zapasowej grupy woluminu
* Edytowanie grupy woluminu
* Usuwanie grupy woluminu

Wszystkie te działania są również dostępne na **akcje** okienka.

## <a name="view-volume-groups"></a>Wyświetlanie grup woluminu
Jeśli klikniesz przycisk **grup woluminu** węzła, **wyniki** w okienku zostaną wyświetlone następujące informacje dotyczące każdej grupy woluminu, w zależności od opcji kolumny wprowadzeniu. (W kolumnach **wyniki** okienku są konfigurowane. Kliknij prawym przyciskiem myszy **woluminów** węzła, wybierz opcję **widoku**, a następnie wybierz **Dodaj/Usuń kolumny**.)

| Kolumny wyników | Opis |
|:--- |:--- |
| Name (Nazwa) |**Nazwa** kolumna zawiera nazwę grupy woluminu. |
| Aplikacja |**Aplikacji** kolumnie jest wyświetlana liczba składniki zapisywania usługi VSS aktualnie zainstalowana i uruchomiona na hoście systemu Windows. |
| Wybrano |**Wybrane** kolumna zawiera wiele woluminów, które są zawarte w grupie woluminu. Wartość zero (0) oznacza, że żadna aplikacja nie jest skojarzony z woluminów w grupie woluminu. |
| Zaimportowane |**Zaimportowane** kolumnie jest wyświetlana liczba importowanych woluminów. Jeśli wartość **True**, ta kolumna wskazuje, że grupa woluminu została zaimportowana z portalu Azure i programu StorSimple Snapshot Manager nie został utworzony. |

> [!NOTE]
> Grupy woluminu StorSimple Snapshot Manager również są wyświetlane na **zasady tworzenia kopii zapasowej** kartę w portalu Azure.
> 
> 

## <a name="create-a-volume-group"></a>Utwórz grupę woluminu
Poniższa procedura umożliwia utworzenie grupy woluminu.

#### <a name="to-create-a-volume-group"></a>Aby utworzyć grupę woluminu
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **grup woluminu**, a następnie kliknij przycisk **Utwórz grupę woluminu**.
   
    ![Utwórz grupę woluminu](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    **Utwórz grupę woluminu** zostanie wyświetlone okno dialogowe.
   
    ![Tworzenie okna dialogowego grupy woluminu](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Wprowadź następujące informacje:
   
   1. W **nazwa** wpisz unikatową nazwę dla nowej grupy woluminu.
   2. W **aplikacji** polu Wybierz aplikacji skojarzonych z woluminów, które ma być dodany do grupy woluminu.
      
       **Aplikacji** okno wyświetla tylko te aplikacje, które korzysta z woluminów StorSimple i mieć składniki zapisywania usługi VSS są włączone dla nich. Składnik zapisywania usługi VSS jest włączona tylko wtedy, gdy wszystkie woluminy, które moduł zapisujący zna woluminów StorSimple. Jeśli pole aplikacji jest pusta, aplikacje, nie korzysta z woluminów Azure StorSimple, które mają być obsługiwane składniki zapisywania usługi VSS są zainstalowane. (Aktualnie Azure StorSimple obsługuje program Microsoft Exchange i SQL Server). Aby uzyskać więcej informacji na temat zapisywania usługi VSS, zobacz [integracji z usługą kopiowania woluminów w tle Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Wybierz aplikację, wszystkie woluminy skojarzone z nim automatycznie zaznaczenie. Z drugiej strony, jeśli wybrano woluminy skojarzone z określoną aplikacją, aplikacja jest automatycznie wybierany w **aplikacji** pole. 
   3. W **woluminów** wybierz woluminy StorSimple, aby dodać do grupy woluminu. 
      
      * Może zawierać woluminy z jednego lub wielu partycji. (Wiele woluminów partycji może być dynamicznych dysków lub dysków podstawowych z wieloma partycjami.) Wolumin, który zawiera wiele partycji jest traktowany jako pojedyncza jednostka. W związku z tym jeśli dodasz do grupy woluminu tylko jedną z partycji, wszystkie pozostałe partycje są automatycznie dodawane do tej grupy woluminu w tym samym czasie. Po dodaniu woluminu wiele partycji w grupie woluminu woluminu wiele partycji w dalszym ciągu traktowane jako pojedyncza jednostka.
      * Możesz tworzyć grupy pusty woluminu, przypisując nie wszystkie woluminy do nich. 
      * Nie należy ich łączyć udostępnione woluminy klastra (CSV), a nie CSV w tej samej grupie woluminu. StorSimple Snapshot Manager nie obsługuje zarówno woluminów CSV i woluminów CSV z systemem innym niż w tej samej migawki.
4. Kliknij przycisk **OK** można zapisać grupy woluminu.

## <a name="back-up-a-volume-group"></a>Tworzenie kopii zapasowej grupy woluminu
Poniższa procedura umożliwia tworzenie kopii zapasowej grupy woluminu.

#### <a name="to-back-up-a-volume-group"></a>Aby utworzyć kopię zapasową woluminu grupy
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **grup woluminu** węzła, kliknij prawym przyciskiem myszy nazwę grupy woluminu, a następnie kliknij przycisk **wykonać kopii zapasowej**.
   
    ![Natychmiast utworzyć kopię zapasową woluminu grupy](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. W **wykonać kopii zapasowej** okno dialogowe, wybierz opcję **migawka lokalna** lub **migawka w chmurze**, a następnie kliknij przycisk **Utwórz**.
   
    ![Zająć kopii zapasowej okna dialogowego](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Aby upewnić się, że kopia zapasowa jest uruchomiony, rozwiń **zadania** węzeł, a następnie kliknij przycisk **systemem**. Tworzenie kopii zapasowej powinien być wyświetlany.
5. Aby wyświetlić ukończone migawki, rozwiń **katalog kopii zapasowej** węzła, rozwinąć nazwę grupy woluminu, a następnie kliknij przycisk **migawka lokalna** lub **migawka w chmurze**. Tworzenie kopii zapasowej będzie wyświetlane, jeśli zostało zakończone pomyślnie.

## <a name="edit-a-volume-group"></a>Edytowanie grupy woluminu
Użyj poniższej procedury do edycji grupy woluminu.

#### <a name="to-edit-a-volume-group"></a>Aby edytować grupę woluminu
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **grup woluminu** węzła, kliknij prawym przyciskiem myszy nazwę grupy woluminu, a następnie kliknij przycisk **Edytuj**.
3. ** Utwórz grupę woluminu ** zostanie wyświetlone okno dialogowe. Możesz zmienić **nazwa**, **aplikacji**, i **woluminów** wpisów.
4. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="delete-a-volume-group"></a>Usuwanie grupy woluminu
Użyj poniższej procedury można usunąć grupy woluminu. 

> [!WARNING]
> Powoduje to również usunięcie wszystkich skojarzonych z grupą woluminu kopii zapasowych.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Aby usunąć grupę woluminu
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **grup woluminu** węzła, kliknij prawym przyciskiem myszy nazwę grupy woluminu, a następnie kliknij przycisk **usunąć**.
3. **Usuń grupę woluminu** zostanie wyświetlone okno dialogowe. Typ **Potwierdź** w polu tekstowym, a następnie kliknij przycisk **OK**.
   
    Grupy usunięte woluminu nieodpowiedniej z listy w **wyniki** okienko i wszystkie kopie zapasowe, które są skojarzone z tą grupą woluminu są usuwane z katalogu kopii zapasowej.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [zarządzać rozwiązania StorSimple przy użyciu programu StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie zasadami tworzenia kopii zapasowej](storsimple-snapshot-manager-manage-backup-policies.md).

