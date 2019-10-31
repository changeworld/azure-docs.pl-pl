---
title: Zarządzaj kopiami zapasowymi & monitorowanie Microsoft Azure Recovery Services
description: Dowiedz się, jak zarządzać kopiami zapasowymi agentów Microsoft Azure Recovery Services i monitorować je za pomocą usługi Azure Backup.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: dacurwin
ms.openlocfilehash: 68c991396c92e6f288d1cc17d6b7dcf1ec79343f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165487"
---
# <a name="manage-microsoft-azure-recovery-services-agent-backups-by-using-the-azure-backup-service"></a>Zarządzanie kopiami zapasowymi Microsoft Azure Recovery Services agenta za pomocą usługi Azure Backup

W tym artykule opisano sposób zarządzania plikami i folderami, których kopię zapasową utworzono przy użyciu agenta Microsoft Azure Recovery Services.

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają, kiedy należy wykonać migawki danych w celu utworzenia punktów odzyskiwania oraz jak długo mają być przechowywane punkty odzyskiwania. Zasady tworzenia kopii zapasowej można skonfigurować przy użyciu agenta MARS.

Utwórz zasady w następujący sposób:

1. Po pobraniu i zarejestrowaniu agenta MARS Uruchom konsolę agenta. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.  
2. W obszarze **Akcje**kliknij pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
3. W Kreatorze harmonogramu tworzenia kopii zapasowych > **wprowadzenie**kliknij przycisk **dalej**.
4. W obszarze **Wybierz elementy do wykonania kopii zapasowej**kliknij pozycję **Dodaj elementy**.

    ![Wybierz elementy do utworzenia kopii zapasowej](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. W obszarze **Wybierz elementy**wybierz, co chcesz utworzyć kopię zapasową, a następnie kliknij przycisk **OK**.

    ![Wybrane elementy do utworzenia kopii zapasowej](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Na stronie **Wybierz elementy do wykonania kopii zapasowej** kliknij przycisk **dalej**.
7. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych** Określ, kiedy mają być wykonywane codziennie lub cotygodniowe kopie zapasowe. Następnie kliknij przycisk **Next** (Dalej).

    - Punkt odzyskiwania jest tworzony podczas tworzenia kopii zapasowej.
    - Liczba punktów odzyskiwania utworzonych w danym środowisku zależy od harmonogramu tworzenia kopii zapasowych.

8. Codzienne kopie zapasowe można zaplanować maksymalnie trzy razy dziennie. Na przykład zrzut ekranu przedstawia dwie codzienne kopie zapasowe, jeden o północy i jeden o godzinie 6:00 PM.

    ![Dzienny harmonogram](./media/backup-configure-vault/day-schedule.png)

9. Można uruchamiać cotygodniowe kopie zapasowe. Na przykład zrzut ekranu pokazuje kopie zapasowe wykonane każdą alternatywną niedzielę & środę o godzinie 9:30 AM i 1:00 AM.

    ![Harmonogram tygodniowy](./media/backup-configure-vault/week-schedule.png)

10. Na stronie **Wybierz zasady przechowywania** Określ, w jaki sposób mają być przechowywane historyczne kopie danych. Następnie kliknij przycisk **Next** (Dalej).

    - Ustawienia przechowywania określają, które punkty odzyskiwania mają być przechowywane, oraz czas ich przechowywania.
    - Na przykład po ustawieniu dziennego ustawienia przechowywania należy wskazać, że w określonym czasie przechowywania najnowszy punkt odzyskiwania będzie przechowywany przez określoną liczbę dni. Innym przykładem może być określenie miesięcznych zasad przechowywania, które wskazują, że punkt odzyskiwania utworzony na 30. miesiąca powinien być przechowywany przez 12 miesięcy.
    - Dzienne i cotygodniowe przechowywanie punktów odzyskiwania zwykle pokrywa się z harmonogramem tworzenia kopii zapasowych. Oznacza to, że gdy kopia zapasowa jest wyzwalana zgodnie z harmonogramem, punkt odzyskiwania utworzony w ramach kopii zapasowej jest przechowywany przez czas określony w zasadach przechowywania dziennego lub tygodniowego.
    - Na przykład na poniższym zrzucie ekranu: — codzienne kopie zapasowe o północy i 6:00 PM są przechowywane przez siedem dni.
            -Kopie zapasowe wykonane w sobotę o północy i 6:00 PM są przechowywane przez cztery tygodnie.
            -Kopie zapasowe wykonane w sobotę w ciągu ostatniego tygodnia miesiąca o północy i 6:00 PM są przechowywane przez 12 miesięcy.
            -Kopie zapasowe wykonane w sobotę w ostatnim tygodniu marca są przechowywane przez 10 lat.

           ![Retention example](./media/backup-configure-vault/retention-example.png)

11. W obszarze **Wybierz typ początkowej kopii zapasowej** Zdecyduj, czy chcesz pobrać początkową kopię zapasową za pośrednictwem sieci, lub Użyj kopii zapasowej offline (Aby uzyskać więcej informacji na temat odwołania do kopii zapasowej offline, zobacz ten [artykuł](backup-azure-backup-import-export.md)). Aby pobrać początkową kopię zapasową za pośrednictwem sieci, wybierz opcję **automatycznie przez sieć** , a następnie kliknij przycisk **dalej**.

    ![Typ początkowej kopii zapasowej](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. W obszarze **potwierdzenie**Przejrzyj informacje, a następnie kliknij przycisk **Zakończ**.
    ![Potwierdź typ kopii zapasowej](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. Po ukończeniu harmonogramu tworzenia kopii zapasowej przez kreatora kliknij przycisk **Zamknij**.
  ![potwierdzić proces modyfikowania kopii zapasowej](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Należy utworzyć zasady na każdym komputerze, na którym zainstalowano agenta.

## <a name="modify-a-backup-policy"></a>Modyfikowanie zasad tworzenia kopii zapasowych

Podczas modyfikowania zasad tworzenia kopii zapasowych można dodać nowe elementy, usunąć istniejące elementy z kopii zapasowej lub wykluczyć pliki z kopii zapasowej za pomocą ustawień wykluczania.

- **Dodaj elementy** Użyj tej opcji tylko w przypadku dodawania nowych elementów do kopii zapasowej. Aby usunąć istniejące elementy, użyj opcji **Usuń elementy** lub **Ustawienia wykluczenia** .  
- **Usuń elementy** Użyj tej opcji, aby usunąć elementy z kopii zapasowej.
  - Użyj **ustawień wykluczeń** , aby usunąć wszystkie elementy w woluminie zamiast **usuwać elementy**.
  - Czyszczenie wszystkich zaznaczeń w woluminie powoduje, że stare kopie zapasowe elementów są zachowywane zgodnie z ustawieniami przechowywania w czasie wykonywania ostatniej kopii zapasowej bez zakresu modyfikacji.
  - Ponowne wybieranie tych elementów powoduje, że nowe kopie zapasowe nie są stosowane do starych kopii zapasowych.
  - Odwybór całego woluminu zachowuje poprzednią kopię zapasową bez żadnego zakresu modyfikowania zasad przechowywania.
- **Ustawienia wykluczania** używają tej opcji, aby wykluczyć konkretne elementy z kopii zapasowej.
  
### <a name="add-new-items-to-existing-policy"></a>Dodawanie nowych elementów do istniejących zasad

1. W obszarze **Akcje**kliknij pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na karcie **Wybierz element zasad** i wybierz pozycję **Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i folderów** , a następnie kliknij przycisk **dalej**.

    ![Wybierz elementy zasad](./media/backup-azure-manage-mars/select-policy-items.png)

3. W obszarze **Modyfikuj lub Zatrzymaj Planowanie kopii zapasowej** wybierz pozycję **Wprowadź zmiany do elementów kopii zapasowej lub godziny** , a następnie kliknij przycisk **dalej**.

    ![Modyfikuj lub Zaplanuj kopię zapasową](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na karcie **Wybierz elementy do utworzenia kopii zapasowej** kliknij pozycję **Dodaj elementy** , aby dodać elementy, dla których chcesz utworzyć kopię zapasową.

    ![Modyfikuj lub Zaplanuj Dodawanie elementów kopii zapasowej](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. W oknie **Wybieranie elementów** wybierz pozycję przepływające lub foldery, które chcesz dodać, a następnie kliknij przycisk **OK**.

    ![Wybierz elementy](./media/backup-azure-manage-mars/select-item.png)

6. Wykonaj kolejne kroki i kliknij przycisk **Zakończ** , aby ukończyć operację.

### <a name="add-exclusion-rules-to-existing-policy"></a>Dodawanie reguł wykluczeń do istniejących zasad

Można dodać reguły wykluczeń, aby pominąć pliki i foldery, których kopie zapasowe nie mają być tworzone. Można to zrobić podczas definiowania nowych zasad lub modyfikowania istniejących zasad.

1. W okienku Akcje kliknij pozycję **Zaplanuj kopię zapasową**. Przejdź do **pozycji Wybierz elementy do kopii zapasowej** , a następnie kliknij pozycję **ustawienia wykluczania**.

    ![Wybierz elementy](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. W obszarze **ustawienia wykluczania**kliknij pozycję **Dodaj wykluczenie**.

    ![Wybierz elementy](./media/backup-azure-manage-mars/add-exclusion.png)

3. Z **pozycji Wybierz elementy do wykluczenia**, Przejrzyj pliki i foldery, a następnie wybierz elementy, które chcesz wykluczyć, i kliknij przycisk **OK**.

    ![Wybierz elementy](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Domyślnie wszystkie **podfoldery** w wybranych folderach są wykluczone. Możesz to zmienić, wybierając opcję **tak** lub **nie**. Można edytować i konkretne typy plików do wykluczenia, jak pokazano poniżej:

    ![Wybierz elementy](./media/backup-azure-manage-mars/subfolders-type.png)

5. Wykonaj kolejne kroki i kliknij przycisk **Zakończ** , aby ukończyć operację.

### <a name="remove-items-from-existing-policy"></a>Usuń elementy z istniejących zasad

1. W okienku Akcje kliknij pozycję **Zaplanuj kopię zapasową**. Przejdź do **pozycji Wybierz elementy do utworzenia kopii zapasowej**. Z listy wybierz pliki i foldery, które chcesz usunąć z harmonogramu kopii zapasowych, a następnie kliknij pozycję **Usuń elementy**.

    ![Wybierz elementy](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Po całkowitym usunięciu woluminu z zasad należy zachować ostrożność.  Jeśli musisz dodać go ponownie, będzie on traktowany jako nowy wolumin. Następna zaplanowana kopia zapasowa wykona początkową kopię zapasową (pełną kopię zapasową) zamiast przyrostowej kopii zapasowej. Jeśli trzeba tymczasowo usunąć i dodać elementy później, zaleca się używanie **ustawień wykluczeń** zamiast **usuwać elementy** , aby zapewnić przyrostową kopię zapasową zamiast pełnej kopii zapasowej.

2. Wykonaj kolejne kroki i kliknij przycisk **Zakończ** , aby ukończyć operację.

## <a name="stop-protecting-files-and-folder-backup"></a>Zatrzymaj ochronę plików i folderów kopii zapasowych

Istnieją dwa sposoby na zatrzymanie ochrony kopii zapasowych plików i folderów:

- **Zatrzymaj ochronę i Zachowaj dane kopii zapasowej**.
  - Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochrony.
  - Usługa Azure Backup zachowa te punkty odzyskiwania, których kopia zapasowa została utworzona na podstawie zasad przechowywania.
  - Będzie można przywrócić dane z kopii zapasowej dla niewygasłych punktów odzyskiwania.
  - Jeśli zdecydujesz się wznowić ochronę, możesz użyć opcji *Włącz ponownie harmonogram tworzenia kopii zapasowych* . Następnie dane byłyby przechowywane na podstawie nowych zasad przechowywania.
- **Zatrzymaj ochronę i Usuń dane kopii zapasowej**.
  - Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochrony danych i usunięcie wszystkich punktów odzyskiwania.
  - Otrzymasz wiadomość e-mail z alertem dotyczącym usuwania danych kopii zapasowej z komunikatem, *że dane dla tego elementu kopii zapasowej zostały usunięte. Te dane będą tymczasowo dostępne przez 14 dni, po upływie których zostanie trwale usunięte* i zalecana Akcja *ponownie Włącz ochronę elementu kopii zapasowej w ciągu 14 dni, aby odzyskać dane.*
  - Aby wznowić ochronę, ponownie Włącz ochronę w ciągu 14 dni od operacji usuwania.

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymywanie ochrony i zachowywanie danych kopii zapasowej

1. Otwórz konsolę zarządzania MARS, przejdź do **okienka Akcje**, a **następnie wybierz pozycję Zaplanuj kopię zapasową**.
    ![zmodyfikować lub zatrzymać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stronie **Wybierz element zasad** wybierz pozycję **Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i folderów, a** następnie kliknij przycisk **dalej**.
    ![zmodyfikować lub zatrzymać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stronie **Modyfikuj lub Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj przy użyciu tego harmonogramu kopii zapasowych, ale Zachowaj przechowywane kopie zapasowe do momentu ponownego aktywowania harmonogramu**. Następnie wybierz opcję **Dalej**.  
    ![zmodyfikować lub zatrzymać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. W obszarze **Wstrzymaj zaplanowaną kopię zapasową** Przejrzyj informacje kliknij przycisk **Zakończ** ![zmodyfikować lub zatrzymać zaplanowane kopie zapasowe.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. w obszarze **Modyfikuj proces tworzenia kopii zapasowej** Sprawdź, czy zaplanowano stan wstrzymania kopii zapasowej i kliknij przycisk **Zamknij** , aby zakończyć

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymywanie ochrony i usuwanie danych kopii zapasowej

1. Otwórz konsolę zarządzania MARS, przejdź do okienka **Akcje** , a następnie wybierz pozycję **Zaplanuj kopię zapasową**.
2. Na stronie **Modyfikuj lub Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj korzystanie z tego harmonogramu kopii zapasowych i Usuń wszystkie przechowywane kopie zapasowe**. Następnie wybierz opcję **Dalej**.

    ![Zmodyfikuj lub Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zakończ**.

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. Aby to zrobić, najpierw Zaloguj się do Azure Portal.
5. Przejdź do **Recovery Services magazynu** > **Ustawienia** > **Właściwości**.
6. W obszarze **zabezpieczający numer PIN**wybierz pozycję **Generuj**. Skopiuj ten kod PIN. Numer PIN jest prawidłowy tylko przez pięć minut.
7. W konsoli zarządzania Wklej kod PIN, a następnie wybierz przycisk **OK**.

    ![Generuj zabezpieczający numer PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikowanie postępu tworzenia kopii zapasowej** zostanie wyświetlony następujący komunikat: *usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuń infrastrukturę tworzenia kopii zapasowych.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj kolejne kroki z portalu.

## <a name="re-enable-protection"></a>Włącz ponownie ochronę

Jeśli ochrona zostanie zatrzymana podczas zachowywania danych i podjęta zostanie decyzja o wznowieniu ochrony, można ponownie włączyć harmonogram tworzenia kopii zapasowych przy użyciu opcji Modyfikuj zasady tworzenia kopii zapasowych.

1. W obszarze **Akcje** wybierz pozycję **Zaplanuj kopię zapasową**.
1. Wybierz pozycję **Włącz ponownie harmonogram tworzenia kopii zapasowych. Możesz również modyfikować elementy kopii zapasowej lub TINES** i klikać przycisk **dalej**.
    ![usunąć infrastrukturę tworzenia kopii zapasowych.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**kliknij przycisk **dalej**.
    ![usunąć infrastrukturę tworzenia kopii zapasowych.](./media/backup-azure-manage-mars/re-enable-next.png)
1. W obszarze **Określ harmonogram kopii zapasowych**Określ harmonogram kopii zapasowych, a następnie kliknij przycisk **dalej**.
1. W obszarze **Wybierz zasady przechowywania**Określ czas trwania przechowywania i kliknij przycisk **dalej**.
1. Na koniec **ekranu,** Przejrzyj szczegóły zasad i kliknij przycisk **Zakończ**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o obsługiwanych scenariuszach i ograniczeniach, zapoznaj się z [macierzą pomocy technicznej dla usługi Mars](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Dowiedz się więcej na temat [zachowania przechowywania zasad tworzenia kopii zapasowych ad hoc](https://docs.microsoft.com/azure/backup/backup-configure-vault#ad-hoc-backup-policy-retention-behavior.md).
