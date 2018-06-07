---
title: Usuwanie magazynu usług odzyskiwania platformy Azure "
description: W tym artykule opisano sposób usuwania magazynu usług odzyskiwania. Artykuł zawiera kroki rozwiązywania problemów, spróbuj usunąć magazynu, ale nie.
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: markgal
ms.openlocfilehash: 844a70aa6fe003c6ad5816aaec9c32db9104c620
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605344"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services
W tym artykule opisano sposób usuwania magazynu usług odzyskiwania w portalu Azure. Jeśli masz magazyny kopii zapasowych, ich został przekonwertowany na magazyny usług odzyskiwania.   

Usuwanie magazynu usług odzyskiwania jest jednoetapowy proces - *pod warunkiem magazynu nie zawiera żadnych zasobów*. Przed usunięciem magazynu usług odzyskiwania, należy usunąć lub usunąć wszystkie zasoby w magazynie. Jeśli próbujesz usunąć magazynu, który zawiera zasoby, występuje błąd, podobnie jak na poniższej ilustracji:

![Błąd usuwania magazynu](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Dopóki zasobów z magazynu zostały wyczyszczone, klikając pozycję **ponów** tworzy ten sam błąd. Jeśli użytkownik pozostaje zablokowany na ten komunikat o błędzie, kliknij przycisk **anulować** i wykonaj następujące kroki, aby usunąć zasoby w magazynie.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Usuwanie elementów z magazynu ochrony maszyn wirtualnych
Jeśli masz już magazyn usług odzyskiwania, Otwórz, przejdź do kroku drugim.

1. Otwórz Azure portal, a z poziomu pulpitu nawigacyjnego magazynu, które chcesz usunąć.

   Jeśli nie masz magazyn usług odzyskiwania przypięty do pulpitu nawigacyjnego, w menu centralnym kliknij **więcej usług** i na liście zasobów wpisz **usług odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij pozycję **Magazyny usługi Recovery Services**.

   ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Zostanie wyświetlona lista magazynów usług odzyskiwania. Z listy wybierz magazyn, który chcesz usunąć.

   ![Wybierz magazyn z listy](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. W widoku magazynu przyjrzeć się **Essentials** okienka. Aby usunąć magazynu, nie może być wszystkie chronione elementy. Jeśli **kopii zapasowej elementów** lub **kopii zapasowej serwerów zarządzania** nie są wyświetlane zera, należy usunąć te elementy. Nie można usunąć magazynu, ponieważ zawiera on dane.

    ![Szukaj w okienku Essentials elementy chronione](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Maszyny wirtualne i pliki i foldery są traktowane jako elementy kopii zapasowej i znajduje się w **kopii zapasowej elementów** obszar okienka Essentials. Serwer DPM znajduje się w **kopii zapasowej serwera zarządzania** obszar okienka Essentials. **Elementy replikowane** odnoszą się do usługi Azure Site Recovery.
3. Aby rozpocząć, usuwanie chronione elementy z magazynu, Znajdź elementy w magazynie. Na pulpicie nawigacyjnym magazynu kliknij **ustawienia**, a następnie kliknij przycisk **kopii zapasowej elementów** do otwarcia tego menu.

    ![Wybierz magazyn z listy](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    **Kopii zapasowej elementów** menu zawiera osobne listy, na podstawie typu elementu: maszyny wirtualne platformy Azure lub folderów plików (zobacz obraz). Lista typu elementu domyślne, wyświetlany jest maszynach wirtualnych platformy Azure. Zaznacz, aby wyświetlić listę elementów folderów plików w magazynie **folderów plików** z menu rozwijanego.
4. Przed usunięciem elementu z magazynu ochrony maszyn wirtualnych, należy zatrzymać zadanie tworzenia kopii zapasowej elementów i usunięcie danych punktu odzyskiwania. Dla każdego elementu w magazynie wykonaj następujące kroki:

    a. Na **elementów kopii zapasowych** menu, kliknij element prawym przyciskiem myszy i wybierz z menu kontekstowego **kopii zapasowej Zatrzymaj**.

    ![Zatrzymaj zadanie tworzenia kopii zapasowej](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Zostanie otwarte menu zatrzymać kopii zapasowej.

    b. Na **zatrzymać kopii zapasowej** menu z **wybierz opcję** menu, wybierz opcję **usunąć danych kopii zapasowej** > wpisz nazwę elementu > i kliknij przycisk **kopii zapasowej Zatrzymaj**.

    Wpisz nazwę elementu, aby sprawdzić, czy chcesz go usunąć. **Zatrzymać kopii zapasowej** aktywuje przycisk po zweryfikowaniu elementu. Jeśli nie ma okno dialogowe, aby wpisać nazwę elementu kopii zapasowej, **Zachowaj dane kopii zapasowej** opcji.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Opcjonalnie możesz podać przyczyny, dlaczego usuwasz danych i Dodaj komentarze. Po kliknięciu **zatrzymać kopii zapasowej**, Zezwalaj Usuń zadanie do wykonania przed próbą usunięcia w magazynie. Aby sprawdzić, czy zadanie zostało zakończone, sprawdź komunikaty Azure ![usunąć danych kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po zakończeniu zadania, usługa wysyła komunikat: Zatrzymano proces tworzenia kopii zapasowej i dane kopii zapasowej zostały usunięte.

    c. Po usunięciu elementu z listy na **kopii zapasowej elementów** menu, kliknij przycisk **Odśwież** pozostałych elementów w magazynie.

      ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienka w menu magazynu usług odzyskiwania. Nie powinny istnieć żadnego **kopii zapasowej elementów**, **kopii zapasowej serwerów zarządzania**, lub **elementy replikowane** na liście. Jeśli nadal wyświetlane w magazynie, wróć do kroku 3 i wybierz typ innego elementu listy.  
5. Jeśli nie ma więcej elementów na pasku narzędzi magazynu, kliknij przycisk **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-vault.png)
6. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a następnie przywraca portalu **nowy** menu usługi.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Co zrobić, jeśli zatrzymano proces tworzenia kopii zapasowej, ale przechowywane dane?
Jeśli jednak przypadkowo zatrzymano proces tworzenia kopii zapasowej *zachowane* danych, należy usunąć dane kopii zapasowej przed usunięciem magazynu. Aby usunąć dane kopii zapasowej:

1. Na **elementów kopii zapasowych** menu, kliknij element prawym przyciskiem myszy, a następnie w menu kontekstowym kliknij **usunąć danych kopii zapasowej**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **Usunąć dane z kopii zapasowej** zostanie otwarte menu.
2. Na **usunąć dane z kopii zapasowej** menu, wpisz nazwę elementu, a następnie kliknij przycisk **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Po usunięciu danych, wróć do kroku 4c i kontynuować proces.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Usuwanie magazynu używane do ochrony serwera programu DPM
Przed usunięciem magazynu używane do ochrony serwera programu DPM, należy wyczyścić punktów odzyskiwania, które zostały utworzone i następnie Wyrejestruj serwer z magazynu.

Aby usunąć dane skojarzone z grupy ochrony:

1. W konsoli administratora programu DPM, kliknij przycisk **ochrony** > Wybierz grupę ochrony > Wybierz członka grupy ochrony > i na wstążce narzędzi kliknij **Usuń**.

  Wybierz członka grupy ochrony, aby aktywować **Usuń** przycisku na wstążce narzędzi. W tym przykładzie element członkowski jest **dummyvm9**. Aby wybrać wiele elementów członkowskich grupy ochrony, przytrzymaj klawisz Ctrl, klikając elementy członkowskie.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    **Zatrzymaj ochronę** zostanie otwarte okno dialogowe.
2. W **Zatrzymaj ochronę** okno dialogowe, wybierz opcję **Usuń chronione dane**i kliknij przycisk **Zatrzymaj ochronę**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Można usunąć magazynu, należy wyczyścić, lub usunąć magazyn chronionych danych. Jeśli istnieje wiele punktów odzyskiwania i danych w grupie ochrony, może upłynąć kilka minut, aby usunąć dane. **Zatrzymaj ochronę** Pokazuje okno dialogowe po zakończeniu zadania.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Kontynuować ten proces dla wszystkich członków we wszystkich grupach ochrony.

    Usuń wszystkie chronione dane i ochronę grupy.
4. Po usunięciu wszystkich członków z grupy ochrony, przełącz się do portalu Azure. Otwórz pulpit nawigacyjny magazynu i upewnij się, że istnieją nie **kopii zapasowej elementów**, **kopii zapasowej serwerów zarządzania**, lub **elementy replikowane**. Kliknij na pasku narzędzi magazynu **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-vault.png)

    W przypadku serwerów zarządzania kopiami zapasowymi w zarejestrowany w magazynie, nie można usunąć magazynu, nawet jeśli nie ma żadnych danych w magazynie. Jeśli usunięto serwerów zarządzania kopiami zapasowymi skojarzonego z magazynem, ale istnieją serwery wymienione w **Essentials** okienku, zobacz [znaleźć serwerów zarządzania kopiami zapasowymi zarejestrowany w magazynie](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a następnie przywraca portalu **nowy** menu usługi.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Usuwanie magazynu używane do ochrony serwera produkcyjnego
Przed usunięciem magazynu używane do ochrony serwera produkcyjnego, należy usunąć lub Wyrejestruj serwer z magazynu.

Aby usunąć serwer produkcyjny skojarzonego z magazynem:

1. W portalu Azure, otwarcia pulpitu nawigacyjnego magazynu, a następnie kliknij przycisk **ustawienia** > **infrastruktura kopii zapasowej** > **serwerów produkcyjnych**.

    ![Otwórz menu serwerów produkcyjnych.](./media/backup-azure-delete-vault/delete-production-server.png)

    **Serwerów produkcyjnych** menu otwiera i wyświetla listę wszystkich serwerów produkcyjnych w magazynie.

    ![Lista serwerów produkcyjnych.](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Na **serwerów produkcyjnych** menu, kliknij prawym przyciskiem myszy na serwerze, a następnie kliknij przycisk **usunąć**.

    ![Usuwanie serwera produkcyjnego ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    **Usunąć** zostanie otwarte menu.

    ![Usuwanie serwera produkcyjnego ](./media/backup-azure-delete-vault/delete-blade.png)
3. Na **usunąć** menu, sprawdź nazwę serwera i kliknij **usunąć**. Należy poprawnie nazwy serwera, aby aktywować **usunąć** przycisku.

    Po usunięciu magazynu, pojawi się komunikat informujący, że magazyn został usunięty. Po usunięciu wszystkich serwerów w magazynie, przewiń do okienka Essentials na pulpicie nawigacyjnym magazynu.
4. Na pulpicie nawigacyjnym magazynu, upewnij się, brak nie **kopii zapasowej elementów**, **kopii zapasowej serwerów zarządzania**, lub **elementy replikowane**. Kliknij na pasku narzędzi magazynu **usunąć**.
5. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a następnie przywraca portalu **nowy** menu usługi.

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Znajdowanie serwerów zarządzania kopii zapasowej, w zarejestrowany w magazynie
Jeśli masz wiele serwerów zarejestrowanych w magazynie, może być trudne do zapamiętania je. Aby wyświetlić serwery zarejestrowane w magazynie i usunąć je:

1. Otwórz pulpit nawigacyjny magazynu.
2. W **Essentials** okienku, kliknij przycisk **ustawienia** do otwarcia tego menu.

    ![otworzyć menu Ustawienia](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Na **ustawienia** menu, kliknij przycisk **infrastruktura kopii zapasowej**.
4. Na **infrastruktura kopii zapasowej** menu, kliknij przycisk **serwerów zarządzania kopiami zapasowymi**. Zostanie otwarte menu serwerów zarządzania kopiami zapasowymi.

    ![Lista serwerów zarządzania kopiami zapasowymi](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Aby usunąć serwer z listy, kliknij prawym przyciskiem myszy nazwę serwera, a następnie kliknij przycisk **usunąć**.
    **Usunąć** zostanie otwarte menu.
6. Na **usunąć** menu, podaj nazwę serwera. Jeśli jest to nazwa długo, można skopiować i wkleić go z listy serwerów zarządzania kopiami zapasowymi. Następnie kliknij pozycję **Usuń**.  
