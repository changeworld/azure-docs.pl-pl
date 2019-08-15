---
title: Usuwanie magazynu Recovery Services na platformie Azure
description: Opisuje sposób usuwania magazynu Recovery Services.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018883"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

W tym artykule opisano sposób usuwania magazynu Recovery Services [Azure Backup](backup-overview.md) . Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwania magazynu.


## <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu Recovery Services, który ma zależności, takie jak serwery chronione lub serwery zarządzania kopiami zapasowymi skojarzone z magazynem.

- Nie można usunąć magazynu zawierającego dane kopii zapasowej (oznacza to, że nawet jeśli została zatrzymana ochrona, ale zachowano dane kopii zapasowej).

- W przypadku usunięcia magazynu, który zawiera zależności, zostanie wyświetlony następujący błąd:

  ![Błąd usuwania magazynu](./media/backup-azure-delete-vault/error.png)

- Po usunięciu lokalnego elementu chronionego (MARS, serwera usługi MAB lub DPM do platformy Azure) z portalu zawierającego zależności zostanie wyświetlony komunikat ostrzegawczy:

  ![Błąd usuwania chronionego serwera](./media/backup-azure-delete-vault/error-message.jpg)

  
Aby bezpiecznie usunąć magazyn, wybierz scenariusz zgodny z konfiguracją i wykonaj zalecane czynności:

Scenariusz | Procedura usuwania zależności do usuwania magazynu |
-- | --
Lokalne pliki i foldery są chronione za pomocą agenta Azure Backup (MARS) kopii zapasowej na platformie Azure | Wykonaj kroki opisane w temacie Usuwanie danych kopii zapasowej i elementy kopii zapasowej — [dla agenta Mars](#delete-backup-items-from-mars-management-console)
Mam lokalne maszyny chronione za pomocą serwera usługi MAB (Microsoft Azure Backup Server) lub DPM na platformie Azure (System Center Data Protection Manager) | Wykonaj kroki opisane w temacie Usuwanie danych kopii zapasowej i elementów kopii zapasowej — [dla agenta serwera usługi MAB](#delete-backup-items-from-mabs-management-console)
Mam chronione elementy w chmurze (np. Maszyna wirtualna w laaS, udział plików platformy Azure itd.)  | Wykonaj kroki opisane w temacie Usuwanie danych kopii zapasowej i elementy kopii zapasowej — [dla chronionych elementów w chmurze](#delete-protected-items-in-cloud)
Mam chronione elementy zarówno lokalnie, jak i w chmurze | Wykonaj kroki opisane w sekcji Usuwanie danych kopii zapasowej i wykonywanie kopii zapasowych w następującej kolejności: <br> - [Dla elementów chronionych w chmurze](#delete-protected-items-in-cloud)<br> - [Agent MARS](#delete-backup-items-from-mars-management-console) <br> - [Dla agenta serwera usługi MAB](#delete-backup-items-from-mabs-management-console)
Nie mam żadnych chronionych elementów lokalnie lub w chmurze; Mimo to nadal otrzymuję błąd usuwania magazynu | Wykonaj kroki opisane w temacie [Usuwanie magazynu Recovery Services przy użyciu klienta Azure Resource Manager](#delete-the-recovery-services-vault-using-azure-resource-manager-client)


## <a name="delete-protected-items-in-cloud"></a>Usuń chronione elementy w chmurze

Przed kontynuowaniem Przeczytaj **[tę](#before-you-start)** sekcję, aby poznać zależności i proces usuwania magazynu.

Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1. Z poziomu portalu >**elementy kopii zapasowej** **magazynu** > Recovery Services, wybierz elementy chronione w chmurze (na przykład maszyna AzureVirtual, usługa Azure Storage (Azure Files), SQL na maszynie wirtualnej platformy Azure itd.).

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kliknij prawym przyciskiem myszy element kopii zapasowej. W zależności od tego, czy element kopii zapasowej jest chroniony, menu wyświetli pozycję **Zatrzymaj tworzenie kopii** zapasowej lub **Usuń dane kopii zapasowej**.

    - Na stronie **Zatrzymaj tworzenie kopii**zapasowej wybierz pozycję **Usuń dane kopii zapasowej** z listy rozwijanej. Wprowadź **nazwę** elementu kopii zapasowej (z uwzględnieniem wielkości liter),wybierz przyczynę, wprowadź **Komentarze**, a następnie kliknij przycisk **Zatrzymaj kopię zapasową**.

        ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-item.png)

    - W polu **Usuń dane kopii zapasowej**wprowadź nazwę elementu kopii zapasowej (z uwzględnieniem wielkości liter), wybierz **przyczynę**, wprowadź **Komentarze**, a następnie kliknij przycisk **Usuń**. 

         ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Sprawdź **powiadomienie** ![Usuń dane](./media/backup-azure-delete-vault/messages.png)kopii zapasowej. Po zakończeniu usługa wyświetli komunikat: **Zatrzymywanie tworzenia kopii zapasowej i usuwanie danych kopii zapasowej dla "*elementu kopii zapasowej*"** . **Pomyślnie ukończono operację**.
6. Kliknij przycisk **Odśwież** w menu **elementy kopii zapasowej** , aby sprawdzić, czy element kopii zapasowej został usunięty.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Usuń chronione elementy lokalnie

Przed przeczytaniem dalszej części **[tej](#before-you-start)** sekcji znajdziesz informacje o zależnościach i procesie usuwania magazynu.

1. W menu pulpitu nawigacyjnego magazynu kliknij pozycję **infrastruktura zapasowa**.
2. W zależności od scenariusza lokalnego wybierz poniższą opcję:

      - W polu **Agent Azure Backup**wybierz pozycję **serwery** > chronione**Azure Backup Agent** i wybierz serwer, który chcesz usunąć. 

        ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - W przypadku **Azure Backup Server**/**programu DPM**wybierz opcję **kopie zapasowe serwerów zarządzania**. Wybierz serwer, który chcesz usunąć. 


          ![Wybierz magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Zostanie wyświetlony blok **Usuń** z komunikatem ostrzegawczym.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server.png)

     Przejrzyj komunikat ostrzegawczy i instrukcje zawarte w polu wyboru wyrażanie zgody.
    
    > [!NOTE]
    >- Jeśli chroniony serwer jest zsynchronizowany z usługą platformy Azure i istnieją elementy kopii zapasowej, w polu wyboru zgoda zostanie wyświetlona liczba zależnych elementów kopii zapasowej oraz link umożliwiający wyświetlenie elementów kopii zapasowej.
    >- Jeśli chroniony serwer nie jest zsynchronizowany z usługą platformy Azure i istnieją elementy kopii zapasowej, w polu wyboru zgoda zostanie wyświetlona liczba elementów kopii zapasowej.
    >- Jeśli elementy kopii zapasowej nie istnieją, to pole wyboru zgoda zostanie poproszony o usunięcie.

4. Zaznacz pole wyboru wyrażanie zgody i kliknij przycisk **Usuń**.




5. Sprawdź **powiadomienie** ![Usuń dane](./media/backup-azure-delete-vault/messages.png)kopii zapasowej. Po zakończeniu usługa wyświetli komunikat: **Zatrzymywanie tworzenia kopii zapasowej i usuwanie danych kopii zapasowej dla "*elementu kopii zapasowej*"** . **Pomyślnie ukończono operację**.
6. Kliknij przycisk **Odśwież** w menu **elementy kopii zapasowej** , aby sprawdzić, czy element kopii zapasowej został usunięty.

Teraz możesz wykonać operację usuwania elementów kopii zapasowej z konsoli zarządzania:
    
   - [Elementy chronione za pomocą usługi MARS](#delete-backup-items-from-mars-management-console)
    - [Elementy chronione za pomocą serwera usługi MAB](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Usuń elementy kopii zapasowej z konsoli zarządzania MARS

Aby usunąć elementy kopii zapasowej z konsoli zarządzania MARS

- Uruchom konsolę zarządzania MARS, przejdź do okienka **Akcje** i wybierz pozycję Zaplanuj **kopię zapasową**.
- W obszarze **Modyfikowanie lub zatrzymywanie kreatora zaplanowanej kopii zapasowej** wybierz opcję **Zatrzymaj korzystanie z tego harmonogramu kopii zapasowych i Usuń wszystkie przechowywane kopie zapasowe** , a następnie kliknij przycisk **dalej**.

    ![Modyfikowanie lub zatrzymywanie zaplanowanej kopii zapasowej](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- W obszarze **Zatrzymaj kreatora zaplanowanej kopii zapasowej** kliknij przycisk **Zakończ**.

    ![Zatrzymaj zaplanowaną kopię zapasową](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Zostanie wyświetlony monit o wprowadzenie zabezpieczającego numeru PIN. Aby wygenerować numer PIN, wykonaj poniższe czynności:
  - Zaloguj się do Portalu Azure.
  - Przejdź do **Recovery Services** > **Właściwości** **ustawień** > magazynu.
  - W obszarze **zabezpieczający numer PIN**kliknij przycisk **Generuj**. Skopiuj ten kod PIN. (Ten kod PIN jest prawidłowy tylko przez pięć minut).
- W konsoli zarządzania programu (aplikacja kliencka) Wklej numer PIN, a następnie kliknij przycisk **OK**.

  ![Zabezpieczający kod PIN](./media/backup-azure-delete-vault/security-pin.png)

- W kreatorze **modyfikowania postępu tworzenia kopii zapasowej** zostaną wyświetlone *usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuwanie infrastruktury kopii zapasowych](./media/backup-azure-delete-vault/deleted-backup-data.png)

Teraz, gdy elementy kopii zapasowej zostały usunięte z lokalizacji lokalnej, wykonaj kolejne kroki z portalu.

### <a name="delete-backup-items-from-mabs-management-console"></a>Usuń elementy kopii zapasowej z konsoli zarządzania serwera usługi MAB

Aby usunąć elementy kopii zapasowej z konsoli zarządzania serwera usługi MAB

**Metoda 1** Aby zatrzymać ochronę i usunąć dane kopii zapasowej, wykonaj następujące czynności:

1.  W Konsola administratora programu DPM, kliknij przycisk **Ochrona** na pasku nawigacyjnym.
2.  W okienku wyświetlania wybierz członka grupy ochrony, który chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wybrać opcję **Zatrzymaj ochronę grup członków** .
3.  W oknie dialogowym zatrzymywanie **ochrony** zaznacz pole wyboru **Usuń chronione dane** > **Usuń magazyn online** , a następnie kliknij przycisk **Zatrzymaj ochronę**.

    ![Usuwanie magazynu w trybie online](./media/backup-azure-delete-vault/delete-storage-online.png)

Stan chronionego elementu członkowskiego jest terazzmieniony na nieaktywną replikę.

4. Kliknij prawym przyciskiem myszy nieaktywną grupę ochrony i wybierz polecenie **Usuń nieaktywną ochronę**.

    ![Usuń nieaktywną ochronę](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. W oknie **usuwanie nieaktywnej ochrony** wybierz pozycję **Usuń magazyn online** , a następnie kliknij przycisk **OK**.

    ![Usuwanie replik na dysku i w trybie online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metoda 2** Uruchom konsolę **zarządzania serwera usługi MAB** . W sekcji **Wybierz metodę ochrony danych** Usuń zaznaczenie opcji **Chcę chronić w trybie online**.

  ![Wybierz metodę ochrony danych](./media/backup-azure-delete-vault/data-protection-method.png)

Teraz, gdy elementy kopii zapasowej zostały usunięte z lokalizacji lokalnej, wykonaj kolejne kroki z portalu.


## <a name="delete-the-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

1. Po usunięciu wszystkich zależności przejdź do okienka **podstawowe** w menu magazyn.
2. Sprawdź, czy na liście nie ma żadnych **elementów kopii zapasowych**, **serwerów zarządzania kopiami zapasowymi**lub **zreplikowanych elementów** . Jeśli elementy nadal pojawiają się w magazynie, zapoznaj się z sekcją [przed rozpoczęciem](#before-you-start) .

3. Jeśli w magazynie nie ma więcej elementów, na pulpicie nawigacyjnym magazynu kliknij pozycję **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**. Magazyn zostanie usunięty, a Portal powróci do menu **Nowa** usługa.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Usuwanie magazynu Recovery Services przy użyciu klienta Azure Resource Manager

Ta opcja usuwania magazynu Recovery Services jest zalecana tylko wtedy, gdy wszystkie zależności zostaną usunięte i nadal pojawia się *błąd usuwania magazynu*.

- W okienku **podstawy** w menu magazyn Sprawdź, czy na liście nie ma żadnych **elementów kopii**zapasowych, **serwerów zarządzania kopiami zapasowymi**ani **zreplikowanych elementów** . Jeśli istnieją elementy kopii zapasowej, zapoznaj się z sekcją [przed rozpoczęciem](#before-you-start) .
- Ponów próbę [usunięcia magazynu z portalu](#delete-the-recovery-services-vault).
- Jeśli wszystkie zależności zostaną usunięte i nadal pojawia się *błąd usuwania magazynu*, użyj narzędzia ARMClient, aby wykonać kroki podane poniżej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Zainstaluj czekoladę z tego [miejsca](https://chocolatey.org/) i zainstaluj ARMClient Uruchom następujące polecenie:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Zaloguj się do konta platformy Azure i uruchom następujące polecenie:

    `ARMClient.exe login [environment name]`

3. W Azure Portal Zbierz Identyfikator subskrypcji i nazwę grupy zasobów dla magazynu, który chcesz usunąć.

Aby uzyskać więcej informacji na temat polecenia ARMClient, [](https://github.com/projectkudu/ARMClient/blob/master/README.md)zapoznaj się z tym dokumentem.

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usuwanie magazynu Recovery Services przy użyciu klienta Azure Resource Manager

1. Uruchom następujące polecenie, używając identyfikatora subskrypcji, nazwy grupy zasobów i nazwy magazynu. Po uruchomieniu polecenia usuwa magazyn, jeśli nie ma żadnych zależności.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Jeśli magazyn nie jest pusty, zostanie wyświetlony komunikat o błędzie "nie można usunąć magazynu, ponieważ w tym magazynie znajdują się zasoby". Aby usunąć chronione elementy/kontener w magazynie, wykonaj następujące czynności:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. W Azure Portal Sprawdź, czy magazyn został usunięty.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny Recovery Services.<br/>
[Informacje na temat](backup-azure-manage-windows-server.md) monitorowania Recovery Services magazynów i zarządzania nimi.
