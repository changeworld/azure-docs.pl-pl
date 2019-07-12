---
title: Monitorowanie baz danych programu SQL Server na maszynie Wirtualnej platformy Azure, którego kopia zapasowa jest tworzona kopia zapasowa Azure i zarządzanie nim | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zarządzać i monitorować baz danych programu SQL Server, które są uruchomione na Maszynie wirtualnej platformy Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: 913140a51603429e003f04f860bca9b4ddb1c214
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704878"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Zarządzanie i monitorowanie kopii zapasowej bazy danych programu SQL Server

W tym artykule opisano typowe zadania dla zarządzania i monitorowania baz danych serwera SQL są uruchamiane na maszynie wirtualnej platformy Azure (VM) i który tworzy kopię zapasową do usługi Azure Backup Recovery Services vault przez [kopia zapasowa Azure](backup-overview.md) usługi. Dowiesz się, jak monitorować zadania i alerty, Zatrzymaj i wznowić ochronę bazy danych, uruchamianie zadań tworzenia kopii zapasowej i wyrejestruj Maszynę wirtualną z kopii zapasowych.

Jeśli jeszcze nie skonfigurowano tworzenia kopii zapasowych baz danych programu SQL Server, zobacz [Utwórz kopię zapasową bazy danych SQL Server na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorowanie ręcznych zadań tworzenia kopii zapasowej w portalu

Usługa Azure Backup pokazuje wszystkie zadania wyzwalanych ręcznie w **zadania tworzenia kopii zapasowej** portalu. Zadania widoczne w tym portalu dołączania bazy danych odnajdywania i rejestrowania i kopii zapasowej i przywracanie operacji.

![Portal zadania kopii zapasowej](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **Zadania tworzenia kopii zapasowej** portal nie wyświetla zaplanowanych zadań kopii zapasowej. Do monitorowania zaplanowanych zadań kopii zapasowych należy użyć programu SQL Server Management Studio zgodnie z opisem w następnej sekcji.
>

Aby uzyskać szczegółowe informacje dotyczące scenariuszy monitorowania, przejdź do [monitorowania w witrynie Azure portal](backup-azure-monitoring-built-in-monitor.md) i [monitorowanie za pomocą usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Ponieważ kopie zapasowe dziennika występuje co 15 minut, monitorowanie zadań tworzenia kopii zapasowej może być żmudne. Usługa Azure Backup ułatwia monitorowania przez wysłanie wiadomości e-mail dla alertów. Alerty poczty e-mail są:

- Wywołany przez wszystkie błędy tworzenia kopii zapasowej.
- Skonsolidowane na poziomie bazy danych przez kod błędu.
- Wysyłane tylko w przypadku pierwszego niepowodzenia wykonywania kopii zapasowej bazy danych.

Aby monitorować alerty kopii zapasowej bazy danych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Na pulpicie nawigacyjnym magazynu wybierz **alerty i zdarzenia**.

   ![Wybieranie pozycji Alerty i zdarzenia](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. W **alerty i zdarzenia**, wybierz opcję **alerty kopii zapasowej**.

   ![Wybieranie pozycji Alerty kopii zapasowej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymywanie ochrony bazy danych programu SQL Server

Aby zatrzymać, tworzenie kopii zapasowej bazy danych programu SQL Server na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej i Usuń wszystkie punkty odzyskiwania.
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i punktów odzyskiwania należy pozostawić bez zmian.

Jeśli zdecydujesz się pozostawienie punktów odzyskiwania, pamiętać o tych szczegółów:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone nieskończoność, oczyszczenia wszystkich są zatrzyma, Zatrzymaj ochronę za pomocą zachowanych danych.
* Opłata wyniesie dla chronionego wystąpienia i użytego miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Jeśli usuniesz źródła danych bez konieczności zatrzymywania tworzenia kopii zapasowych nowych kopii zapasowych zakończy się niepowodzeniem.

Aby zatrzymać ochronę bazy danych:

1. Na pulpicie nawigacyjnym magazynu wybierz **elementy kopii zapasowej**.

2. W obszarze **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Wybierz bazę danych, dla którego chcesz zatrzymać ochronę.

    ![Wybieranie bazy danych w celu zatrzymania ochrony](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. W menu bazy danych wybierz **Zatrzymaj kopię zapasową**.

    ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-db-button.png)


5. Na **Zatrzymaj tworzenie kopii zapasowej** menu, wybierz, czy chcesz zachować lub usunąć dane. Jeśli chcesz, podaj przyczynę i komentarz.

    ![Zachować lub usunąć dane w menu Zatrzymaj kopię zapasową](./media/backup-azure-sql-database/stop-backup-button.png)

6. Wybierz **Zatrzymaj kopię zapasową**.

>
> [!NOTE]
Zobacz poniżej — często zadawane pytania, aby uzyskać więcej informacji na temat usuwania danych opcji: <br/>
* [Jeśli usunę bazę danych z wystąpienia autoprotected, co się stanie z kopii zapasowych?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)<br/>
* [Jeśli operacji tworzenia kopii zapasowej bazy danych autoprotected są zatrzymywane, jaka będzie jego zachowanie?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Po zatrzymaniu ochrony usługi SQL database, jeśli zostanie wybrana **Zachowaj dane kopii zapasowej** opcji później można wznowić ochronę. Jeśli nie Zachowaj dane kopii zapasowej, nie można wznowić ochronę.

Aby wznowić ochronę bazy danych SQL:

1. Otwórz element kopii zapasowej i wybrać **Wznów tworzenie kopii zapasowej**.

    ![Wybieranie pozycji Wznów tworzenie kopii zapasowej w celu wznowienia ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

2. W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie

Można uruchomić poszczególnych typów kopii zapasowych na żądanie:

* Pełna kopia zapasowa
* Pełna kopia zapasowa tylko do kopiowania
* Różnicowa kopia zapasowa
* Kopia zapasowa dziennika

Gdy należy określić okres przechowywania dla Copy-only pełnej kopii zapasowej, zakres przechowywania dla innych typów kopii zapasowych jest automatycznie równa 30 dni od bieżącego czasu. <br/>
Aby uzyskać więcej informacji, zobacz [typy kopii zapasowych programu SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Po wyłączeniu ochrony, ale przed usunięciem magazynu, należy wyrejestrować wystąpienia programu SQL Server:

1. Na pulpicie nawigacyjnym magazynu w ramach **Zarządzaj**, wybierz opcję **infrastruktura zapasowa**.  

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. W obszarze **Serwery zarządzania** wybierz pozycję **Serwery chronione**.

   ![Wybieranie pozycji Serwery chronione](./media/backup-azure-sql-database/protected-servers.png)

3. W **serwerów chronionych**, wybierz serwer, które chcesz wyrejestrować. Aby usunąć magazyn, musisz wyrejestrować wszystkie serwery.

4. Kliknij prawym przyciskiem myszy serwer chroniony, a następnie wybierz pozycję **Wyrejestruj**.

   ![Wybieranie pozycji Usuń](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Ponowne zarejestrowanie rozszerzenia na maszynie Wirtualnej programu SQL Server

Czasami rozszerzenia obciążeń na maszynie Wirtualnej może mieć wpływ na jedną z przyczyn lub innych. W takiej sytuacji wszystkie operacje, które są wyzwalane na maszynie Wirtualnej rozpocznie się nie powiedzie się. Następnie może być konieczne ponowne zarejestrowanie rozszerzenia na maszynie Wirtualnej. **Zarejestruj ponownie** operacji ponownie instaluje rozszerzenie kopii zapasowej obciążeń na maszynie Wirtualnej dla operacji kontynuować.  <br>

Zaleca się użycie tej opcji z ostrożnością; Po wyzwoleniu na maszynie Wirtualnej z rozszerzeniem już w dobrej kondycji, ta operacja spowoduje, że rozszerzenie Aby uzyskać ponownego uruchomienia. Może to spowodować, że wszystkie zadania w toku, nie powiedzie się. Zajrzyj do co najmniej jeden z [objawy](backup-sql-server-azure-troubleshoot.md#re-registration-failures) przed wyzwoleniem operacji ponownej rejestracji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z kopiami zapasowymi w bazie danych programu SQL Server](backup-sql-server-azure-troubleshoot.md).
