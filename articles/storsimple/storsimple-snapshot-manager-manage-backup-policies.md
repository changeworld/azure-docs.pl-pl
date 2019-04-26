---
title: Zasady tworzenia kopii zapasowych programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób używania przystawki MMC przystawki StorSimple Snapshot Manager do tworzenia i zarządzania zasadami tworzenia kopii zapasowych, które kontrolują zaplanowanych kopii zapasowych.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 7dac26b058b959011e38b4373369b8a1115d2705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303271"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Tworzenie i zarządzanie zasadami kopii zapasowych za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Zasady tworzenia kopii zapasowej tworzy harmonogram tworzenia kopii zapasowych danych woluminu, lokalnie lub w chmurze. Po utworzeniu zasad tworzenia kopii zapasowej, można również określić zasady przechowywania. (Można przechowywać maksymalnie 64 migawki). Aby uzyskać więcej informacji na temat zasad tworzenia kopii zapasowych, zobacz [kopii zapasowej typy](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) w [serii StorSimple 8000: hybrydowe rozwiązanie w chmurze](storsimple-overview.md).

W tym samouczku wyjaśniono:

* Tworzenie zasad kopii zapasowych
* Edytowanie zasad tworzenia kopii zapasowej
* Usuwanie zasad kopii zapasowych

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych
Poniższa procedura umożliwia utworzenie nowych zasad tworzenia kopii zapasowej.

#### <a name="to-create-a-backup-policy"></a>Aby utworzyć zasady kopii zapasowych
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **zasady tworzenia kopii zapasowych**i kliknij przycisk **zasady tworzenia kopii zapasowej**.

    ![Tworzenie zasad kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **Utwórz zasadę** pojawi się okno dialogowe.

    ![Tworzenie zasad — karta Ogólne](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na **ogólne** karcie, wykonaj następujące informacje:

   1. W **nazwa** polu tekstowym wpisz nazwę zasad.
   2. W **grupy woluminów** pole tekstowe, wpisz nazwę grupy woluminów skojarzonych z zasadami.
   3. Wybierz opcję **migawka lokalna** lub **migawki w chmurze**.
   4. Wybierz liczbę migawek do zachowania. Jeśli wybierzesz **wszystkich**, 64 migawki staną się przechowywane (maksymalna).
4. Kliknij przycisk **harmonogram** kartę.

    ![Tworzenie zasad — karta harmonogram](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na **harmonogram** karcie, wykonaj następujące informacje:

   1. Kliknij przycisk **Włącz** pole wyboru, aby zaplanować następną kopią zapasową.
   2. W obszarze **ustawienia**, wybierz opcję **jeden raz**, **codzienne**, **tygodniowy**, lub **miesięczne**.
   3. W **Start** pola tekstowego, kliknij ikonę kalendarza i wybierz datę rozpoczęcia.
   4. W obszarze **Zaawansowane ustawienia**, można ustawić opcjonalny powtórzeń harmonogramów oraz datę zakończenia.
   5. Kliknij przycisk **OK**.

Po utworzeniu zasad tworzenia kopii zapasowej, zostaną wyświetlone następujące informacje w **wyniki** okienka:

* **Nazwa** — Nazwa zasad kopii zapasowych.
* **Typ** — migawka lokalna i migawka w chmurze.
* **Grupa woluminów** — grupa woluminów skojarzonych z zasadami.
* **Przechowywanie** — liczby migawek zachowywany; maksymalna liczba to 64.
* **Utworzone** — datę, która zasada została utworzona.
* **Włączone** — Określa, czy zasada jest obecnie w efekcie: **Wartość true,** wskazuje, że jest obowiązywały; **False** wskazuje, że nie będzie obowiązywać.

## <a name="edit-a-backup-policy"></a>Edytowanie zasad tworzenia kopii zapasowej
Poniższa procedura umożliwia Edytuj istniejące zasady tworzenia kopii zapasowej.

#### <a name="to-edit-a-backup-policy"></a>Aby edytować zasady tworzenia kopii zapasowej
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij **zasady tworzenia kopii zapasowych** węzła. Zasady tworzenia kopii zapasowych są wyświetlane w **wyniki** okienka.
3. Kliknij prawym przyciskiem myszy zasady, które chcesz edytować, a następnie kliknij przycisk **Edytuj**.

    ![Edytowanie zasad tworzenia kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Gdy **Utwórz zasadę** zostanie wyświetlone okno, wprowadź zmiany, a następnie kliknij przycisk **OK**.

## <a name="delete-a-backup-policy"></a>Usuwanie zasad kopii zapasowych
Użyj poniższej procedury można usunąć zasad tworzenia kopii zapasowej.

#### <a name="to-delete-a-backup-policy"></a>Aby usunąć zasady kopii zapasowych
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij **zasady tworzenia kopii zapasowych** węzła. Zasady tworzenia kopii zapasowych są wyświetlane w **wyniki** okienka.
3. Kliknij prawym przyciskiem myszy zasady kopii zapasowych, który chcesz usunąć, a następnie kliknij przycisk **Usuń**.
4. Po wyświetleniu komunikatu potwierdzenia kliknij **tak**.

    ![Zasady tworzenia kopii zapasowej potwierdzenie usunięcia](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [do przeglądania i zarządzania zadania tworzenia kopii zapasowej za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-jobs.md).
