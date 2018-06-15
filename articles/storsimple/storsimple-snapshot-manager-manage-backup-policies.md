---
title: Zasady tworzenia kopii zapasowej programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania przystawki MMC programu StorSimple Snapshot Manager do tworzenia i obsługi zasad tworzenia kopii zapasowych, które kontrolują zaplanowanych kopii zapasowych.
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
ms.openlocfilehash: 218c89e403673c16c72da95aa2c1d685bbed5a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875473"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie zasadami tworzenia kopii zapasowej
## <a name="overview"></a>Omówienie
Zasady tworzenia kopii zapasowej tworzy harmonogram tworzenia kopii zapasowych danych woluminu lokalnie lub w chmurze. Podczas tworzenia zasad tworzenia kopii zapasowej, można także określić zasady przechowywania. (Można zachować maksymalnie 64 migawki). Aby uzyskać więcej informacji na temat zasad tworzenia kopii zapasowych, zobacz [kopii zapasowej typy](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) w [z serii StorSimple 8000: rozwiązanie chmury hybrydowej](storsimple-overview.md).

Ten samouczek wyjaśnia, jak:

* Tworzenie zasad tworzenia kopii zapasowej
* Edytowanie zasad tworzenia kopii zapasowej
* Usuń zasady tworzenia kopii zapasowej

## <a name="create-a-backup-policy"></a>Tworzenie zasad tworzenia kopii zapasowej
Poniższa procedura umożliwia utworzenie nowych zasad tworzenia kopii zapasowej.

#### <a name="to-create-a-backup-policy"></a>Aby utworzyć zasady tworzenia kopii zapasowej
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **zasady tworzenia kopii zapasowej**i kliknij przycisk **zasad tworzenia kopii zapasowej**.

    ![Tworzenie zasad tworzenia kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **Utworzyć zasadę** zostanie wyświetlone okno dialogowe.

    ![Utwórz zasadę — karta Ogólne](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na **ogólne** karcie, wykonaj następujące informacje:

   1. W **nazwa** polu tekstowym, wpisz nazwę zasady.
   2. W **grupy woluminu** polu tekstowym, wpisz nazwę grupy woluminu skojarzonych z zasadami.
   3. Wybierz opcję **migawka lokalna** lub **migawki w chmurze**.
   4. Wybierz liczbę migawek do zachowania. W przypadku wybrania **wszystkie**, będzie 64 migawki zachowane (maksymalnie).
4. Kliknij przycisk **harmonogram** kartę.

    ![Utwórz zasadę — karta harmonogram](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na **harmonogram** karcie, wykonaj następujące informacje:

   1. Kliknij przycisk **włączyć** pole wyboru, aby zaplanować następnej kopii zapasowej.
   2. W obszarze **ustawienia**, wybierz pozycję **jeden raz**, **codzienne**, **co tydzień**, lub **miesięczne**.
   3. W **Start** pola tekstowego, kliknij ikonę kalendarza i wybierz datę rozpoczęcia.
   4. W obszarze **Zaawansowane ustawienia**, można ustawić opcjonalny powtarzania harmonogramów oraz datę zakończenia.
   5. Kliknij przycisk **OK**.

Po utworzeniu zasad tworzenia kopii zapasowej następujące informacje są wyświetlane w **wyniki** okienka:

* **Nazwa** — Nazwa zasad tworzenia kopii zapasowej.
* **Typ** — migawka lokalna i migawka w chmurze.
* **Grupy woluminu** — skojarzonych z zasadami grupy woluminu.
* **Przechowywania** — liczba migawki zachowane; maksymalna liczba to 64.
* **Utworzony** — datę, która zasada została utworzona.
* **Włączone** — czy zasada jest obecnie w efekcie: **True** wskazuje, że jest włączona; **False** wskazuje, że nie będzie obowiązywać.

## <a name="edit-a-backup-policy"></a>Edytowanie zasad tworzenia kopii zapasowej
Użyj poniższej procedury do edycji istniejących zasad tworzenia kopii zapasowej.

#### <a name="to-edit-a-backup-policy"></a>Aby edytować zasadę tworzenia kopii zapasowej
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **zasady tworzenia kopii zapasowej** węzła. Zasady tworzenia kopii zapasowej są wyświetlane w **wyniki** okienka.
3. Kliknij prawym przyciskiem myszy zasadę, którą chcesz edytować, a następnie kliknij przycisk **Edytuj**.

    ![Edytowanie zasad tworzenia kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Gdy **utworzyć zasadę** zostanie wyświetlone okno, wprowadź zmiany, a następnie kliknij przycisk **OK**.

## <a name="delete-a-backup-policy"></a>Usuń zasady tworzenia kopii zapasowej
Użyj poniższej procedury można usunąć zasad tworzenia kopii zapasowej.

#### <a name="to-delete-a-backup-policy"></a>Aby usunąć zasady tworzenia kopii zapasowej
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **zasady tworzenia kopii zapasowej** węzła. Zasady tworzenia kopii zapasowej są wyświetlane w **wyniki** okienka.
3. Kliknij prawym przyciskiem myszy zasady tworzenia kopii zapasowej, który chcesz usunąć, a następnie kliknij przycisk **usunąć**.
4. Po wyświetleniu komunikatu potwierdzenia kliknij **tak**.

    ![Zasady tworzenia kopii zapasowej potwierdzenie usunięcia](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązania StorSimple przy użyciu programu StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [StorSimple Snapshot Manager umożliwia wyświetlanie i zarządzanie nimi zadania tworzenia kopii zapasowej](storsimple-snapshot-manager-manage-backup-jobs.md).
