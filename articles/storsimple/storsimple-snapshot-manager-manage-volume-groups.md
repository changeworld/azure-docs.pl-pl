---
title: StorSimple Grupy woluminów Menedżera migawek | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia grup woluminów i zarządzania grupami woluminów za pomocą przystawki Menedżer migawek StorSimple do tworzenia grup woluminów i zarządzania nimi.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 5198729cf96fb48c5dcd05096c04ea4d77c26de5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931490"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Tworzenie grup woluminów i zarządzanie nimi za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Za pomocą węzła **Grupy woluminów** w okienku **Zakres** można przypisywać woluminy do grup woluminów, wyświetlać informacje o grupie woluminów, planować kopie zapasowe i edytować grupy woluminów.

Grupy woluminów to pule powiązanych woluminów używanych w celu zapewnienia, że kopie zapasowe są spójne z aplikacjami. Aby uzyskać więcej informacji, zobacz [Grupy woluminów i woluminów](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) oraz [integracja z usługą kopiowania woluminów w tle systemu Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Wszystkie woluminy w grupie woluminów muszą pochodzić od jednego dostawcy usług w chmurze.
> * Podczas konfigurowania grup woluminów nie należy mieszać woluminów udostępnionych przez klaster (CSV) i innych niż CSV w tej samej grupie woluminów. StorSimple Snapshot Manager nie obsługuje kombinacji csv i innych niż CSV w tej samej migawki.

![Węzeł grup woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Rysunek 1: Węzeł Grup woluminów Menedżera migawek StorSimple** 

W tym samouczku wyjaśniono, jak można użyć StorSimple Snapshot Manager do:

* Wyświetlanie informacji o grupach woluminów
* Tworzenie grupy woluminów
* 3.
* Edytowanie grupy woluminów
* Usuwanie grupy woluminów

Wszystkie te akcje są również dostępne w okienku **Akcje.**

## <a name="view-volume-groups"></a>Wyświetlanie grup woluminów
Po kliknięciu **węzła Grupy woluminów** w okienku **Wyniki** zostaną wyświetlenia następujących informacji o każdej grupie woluminów, w zależności od wybranych kolumn. (Kolumny w okienku **Wyniki** można konfigurować. Kliknij prawym przyciskiem myszy węzeł **Woluminy,** wybierz polecenie **Widok**, a następnie wybierz polecenie **Dodaj/Usuń kolumny.**

| Kolumna Wyniki | Opis |
|:--- |:--- |
| Nazwa |Kolumna **Nazwa** zawiera nazwę grupy woluminów. |
| Aplikacja |Kolumna **Aplikacje** pokazuje liczbę modułów zapisujących usługi VSS aktualnie zainstalowanych i uruchomionych na hoście systemu Windows. |
| Wybrano |Kolumna **Zaznaczone** pokazuje liczbę woluminów zawartych w grupie woluminów. Zero (0) wskazuje, że żadna aplikacja nie jest skojarzona z woluminami w grupie woluminów. |
| Importowane |Kolumna **Importowane** pokazuje liczbę importowanych woluminów. Po ustawieniu wartości **True**ta kolumna wskazuje, że grupa woluminów została zaimportowana z witryny Azure portal i nie została utworzona w Menedżerze migawek StorSimple. |

> [!NOTE]
> StorSimple Snapshot Manager grupy woluminów są również wyświetlane na karcie **Zasady kopii zapasowych** w witrynie Azure portal.
> 
> 

## <a name="create-a-volume-group"></a>Tworzenie grupy woluminów
Aby utworzyć grupę woluminów, użyj poniższej procedury.

#### <a name="to-create-a-volume-group"></a>Aby utworzyć grupę woluminów
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy pozycję **Grupy woluminów,** a następnie kliknij polecenie **Utwórz grupę woluminów**.
   
    ![Tworzenie grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Zostanie **wyświetlone** okno dialogowe Tworzenie grupy woluminów.
   
    ![Tworzenie okna dialogowego grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Wprowadź następujące informacje:
   
   1. W polu **Nazwa** wpisz unikatową nazwę nowej grupy woluminów.
   2. W polu **Aplikacje** wybierz aplikacje skojarzone z woluminami dodania do grupy woluminów.
      
       Pole **Aplikacje** zawiera listę tylko tych aplikacji, które używają woluminów StorSimple i mają włączone moduły zapisu vss dla nich. Moduł zapisujący vss jest włączona tylko wtedy, gdy wszystkie woluminy, które moduł zapisujący jest świadomy są StorSimple woluminów. Jeśli pole Aplikacje jest puste, nie są zainstalowane żadne aplikacje korzystające z woluminów usługi Azure StorSimple i obsługiwane przez moduły zapisu usługi VSS. (Obecnie usługa Azure StorSimple obsługuje program Microsoft Exchange i SQL Server). Aby uzyskać więcej informacji na temat modułów zapisujących usługi VSS, zobacz [Integracja z usługą kopiowania woluminów systemu Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Jeśli wybierzesz aplikację, wszystkie woluminy skojarzone z nią zostaną automatycznie wybrane. Z drugiej strony w przypadku wybrania woluminów skojarzonych z określoną aplikacją aplikacja jest automatycznie wybierana w polu **Aplikacje.** 
   3. W polu **Woluminy** wybierz pozycję StorProszkaj woluminy, które chcesz dodać do grupy woluminów. 
      
      * Woluminy można dołączyć z pojedynczą lub wieloma partycjami. (Wiele woluminów partycji może być dyskami dynamicznymi lub podstawowymi z wieloma partycjami). Wolumin zawierający wiele partycji jest traktowany jako pojedyncza jednostka. W związku z tym jeśli dodasz tylko jedną partycję do grupy woluminów, wszystkie inne partycje są automatycznie dodawane do tej grupy woluminów w tym samym czasie. Po dodaniu woluminu wielu partycji do grupy woluminów wolumin wielu partycji nadal jest traktowany jako pojedyncza jednostka.
      * Puste grupy woluminów można utworzyć, nie przypisując do nich żadnych woluminów. 
      * Nie należy mieszać woluminów udostępnionych przez klaster (CSV) i innych niż CSV w tej samej grupie woluminów. StorSimple Snapshot Manager nie obsługuje kombinacji woluminów CSV i woluminów innych niż CSV w tej samej migawki.
4. Kliknij **przycisk OK,** aby zapisać grupę woluminów.

## <a name="back-up-a-volume-group"></a>3.
Poniższa procedura służy do tworzenia kopii zapasowych grupy woluminów.

#### <a name="to-back-up-a-volume-group"></a>Aby zrobić kopii zapasowej grupy woluminów
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Grupy woluminów,** kliknij prawym przyciskiem myszy nazwę grupy woluminów, a następnie kliknij polecenie **Wykonaj kopię zapasową**.
   
    ![Natychmiast sedli zapasowe grupy woluminów](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. W oknie dialogowym **Wykonywanie kopii zapasowej** wybierz pozycję **Migawka lokalna** lub **Migawka w chmurze,** a następnie kliknij przycisk **Utwórz**.
   
    ![Okno dialogowe wykonywania kopii zapasowej](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Aby potwierdzić, że kopia zapasowa jest uruchomiona, rozwiń węzeł **Zadania,** a następnie kliknij przycisk **Uruchomiona**. Kopia zapasowa powinna być wymieniona.
5. Aby wyświetlić ukończoną migawkę, rozwiń węzeł **Wykaz kopii zapasowych,** rozwiń nazwę grupy woluminów, a następnie kliknij pozycję **Migawka lokalna** lub **Migawka w chmurze**. Kopia zapasowa zostanie wyświetlona, jeśli zakończy się pomyślnie.

## <a name="edit-a-volume-group"></a>Edytowanie grupy woluminów
Aby edytować grupę woluminów, użyj poniższej procedury.

#### <a name="to-edit-a-volume-group"></a>Aby edytować grupę woluminów
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Grupy woluminów,** kliknij prawym przyciskiem myszy nazwę grupy woluminów, a następnie kliknij polecenie **Edytuj**.
3. Zostanie wyświetlone okno dialogowe **Tworzenie grupy woluminów **. Można zmienić **wpisy Nazwa**, **Aplikacje**i **Woluminy.**
4. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="delete-a-volume-group"></a>Usuwanie grupy woluminów
Poniższa procedura służy do usuwania grupy woluminów. 

> [!WARNING]
> Spowoduje to również usunięcie wszystkich kopii zapasowych skojarzonych z grupą woluminów.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Aby usunąć grupę woluminów
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Grupy woluminów,** kliknij prawym przyciskiem myszy nazwę grupy woluminów, a następnie kliknij polecenie **Usuń**.
3. Zostanie **wyświetlone** okno dialogowe Usuwanie grupy woluminów. Wpisz pole tekstowe **Potwierdź,** a następnie kliknij przycisk **OK**.
   
    Usunięta grupa woluminów zniknie z listy w okienku **Wyniki,** a wszystkie kopie zapasowe skojarzone z tą grupą woluminów zostaną usunięte z katalogu kopii zapasowych.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązaniem StorSimple, za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się, jak [tworzyć zasady tworzenia kopii zapasowych za pomocą Menedżera migawek StorSimple i zarządzać nimi.](storsimple-snapshot-manager-manage-backup-policies.md)

