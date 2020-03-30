---
title: Zasady tworzenia kopii zapasowych Menedżera migawek StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania przystawki PROGRAMU MMC Menedżera migawek StorSimple do tworzenia zasad tworzenia kopii zapasowych i zarządzania nimi, które kontrolują zaplanowane kopie zapasowe.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933359"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Zasady tworzenia kopii zapasowych tworzy harmonogram tworzenia kopii zapasowych danych woluminu lokalnie lub w chmurze. Podczas tworzenia zasad tworzenia kopii zapasowych można również określić zasady przechowywania. (Można zachować maksymalnie 64 migawek). Aby uzyskać więcej informacji na temat zasad tworzenia kopii zapasowych, zobacz [Typy kopii zapasowych](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) w [serii StorSimple 8000: rozwiązanie chmury hybrydowej](storsimple-overview.md).

W tym samouczku wyjaśniono:

* Tworzenie zasad kopii zapasowych
* Edytowanie zasad tworzenia kopii zapasowych
* Usuwanie zasad tworzenia kopii zapasowych

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych
Poniższa procedura służy do tworzenia nowych zasad tworzenia kopii zapasowych.

#### <a name="to-create-a-backup-policy"></a>Aby utworzyć zasady tworzenia kopii zapasowych
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy pozycję **Zasady kopii zapasowej,** a następnie kliknij polecenie **Utwórz zasady kopii zapasowej**.

    ![Tworzenie zasad kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Zostanie wyświetlone okno dialogowe **Tworzenie zasad.**

    ![Tworzenie karty Zasady — ogólne](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na karcie **Ogólne** uzupełnij następujące informacje:

   1. W polu tekstowym **Nazwa** wpisz nazwę zasady.
   2. W polu tekstowym **Grupa woluminów** wpisz nazwę grupy woluminów skojarzonej z zasadami.
   3. Wybierz **migawkę lokalną** lub **migawkę w chmurze**.
   4. Wybierz liczbę migawek do zachowania. Jeśli wybierzesz **Wszystkie**, 64 migawki zostaną zachowane (maksymalna).
4. Kliknij kartę **Harmonogram** .

    ![Karta Tworzenie zasad — harmonogram](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na karcie **Harmonogram** uzupełnij następujące informacje:

   1. Kliknij pole wyboru **Włącz,** aby zaplanować następną kopię zapasową.
   2. W obszarze **Ustawienia**wybierz **opcję Jeden raz,** **Codziennie,** **Co tydzień**lub **Co miesiąc**.
   3. W polu tekstowym **Rozpocznij** kliknij ikonę kalendarza i wybierz datę rozpoczęcia.
   4. W obszarze **Ustawienia zaawansowane**można ustawić opcjonalne harmonogramy powtórzeń i datę zakończenia.
   5. Kliknij przycisk **OK**.

Po utworzeniu zasad tworzenia kopii zapasowych w okienku **Wyniki** zostaną wyświetlają następujące informacje:

* **Nazwa** — nazwa zasad tworzenia kopii zapasowych.
* **Typ** — migawka lokalna lub migawka w chmurze.
* **Grupa woluminów** — grupa woluminów skojarzona z zasadami.
* **Retencja** — liczba zachowanych migawek; maksymalna wynosi 64.
* **Utworzono** — datę utworzenia tej zasady.
* **Włączone** — czy zasada jest obecnie w mocy: **True** wskazuje, że jest w mocy; **False** wskazuje, że nie jest w mocy.

## <a name="edit-a-backup-policy"></a>Edytowanie zasad tworzenia kopii zapasowych
Poniższa procedura służy do edytowania istniejących zasad tworzenia kopii zapasowych.

#### <a name="to-edit-a-backup-policy"></a>Aby edytować zasady tworzenia kopii zapasowych
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij węzeł **Zasady kopii zapasowej.** Wszystkie zasady tworzenia kopii zapasowych są wyświetlane w okienku **Wyniki.**
3. Kliknij prawym przyciskiem myszy zasadę, którą chcesz edytować, a następnie kliknij polecenie **Edytuj**.

    ![Edytowanie zasad tworzenia kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Po **wyświetleniu** okna Utwórz zasady wprowadź zmiany, a następnie kliknij przycisk **OK**.

## <a name="delete-a-backup-policy"></a>Usuwanie zasad tworzenia kopii zapasowych
Poniższa procedura służy do usuwania zasad tworzenia kopii zapasowych.

#### <a name="to-delete-a-backup-policy"></a>Aby usunąć zasady tworzenia kopii zapasowych
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij węzeł **Zasady kopii zapasowej.** Wszystkie zasady tworzenia kopii zapasowych są wyświetlane w okienku **Wyniki.**
3. Kliknij prawym przyciskiem myszy zasadę tworzenia kopii zapasowych, którą chcesz usunąć, a następnie kliknij polecenie **Usuń**.
4. Po wyświetleniu komunikatu potwierdzającego kliknij przycisk **Tak**.

    ![Usuwanie potwierdzenia zasad tworzenia kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązaniem StorSimple, za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się, jak [używać Menedżera migawek StorSimple do wyświetlania zadań tworzenia kopii zapasowych i zarządzania nimi.](storsimple-snapshot-manager-manage-backup-jobs.md)
