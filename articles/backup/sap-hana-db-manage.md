---
title: Zarządzanie bazami danych SAP HANA utworzonymi kopię zapasową na maszynach wirtualnych platformy Azure
description: W tym artykule zapoznaj się z typowymi zadaniami dotyczącymi zarządzania i monitorowania baz danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480066"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Zarządzanie kopiami zapasowymi baz danych platformy SAP HANA i ich monitorowanie

W tym artykule opisano typowe zadania dotyczące zarządzania i monitorowania baz danych SAP HANA, które są uruchomione na maszynie wirtualnej platformy Azure (VM) i które są archiwizowane w magazynie usług Azure Backup Recovery Services przez usługę [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview) Dowiesz się, jak monitorować zadania i alerty, wyzwalać kopie zapasowe na żądanie, edytować zasady, zatrzymywać i wznawiać ochronę baz danych oraz wyrejestrować maszynę wirtualną z kopii zapasowych.

Jeśli nie skonfigurowano jeszcze kopii zapasowych dla baz danych SAP HANA, zobacz [Tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorowanie ręcznych zadań tworzenia kopii zapasowych w portalu

Usługa Kopia zapasowa platformy Azure pokazuje wszystkie ręcznie wyzwalane zadania w sekcji **Zadania kopii zapasowej** w witrynie Azure portal.

![Sekcja Zadania tworzenia kopii zapasowych](./media/sap-hana-db-manage/backup-jobs.png)

Zadania widoczne w tym portalu obejmują odnajdowanie i rejestrowanie baz danych oraz operacje tworzenia kopii zapasowych i przywracania. Zaplanowane zadania, w tym kopie zapasowe dziennika nie są wyświetlane w tej sekcji. Ręcznie wyzwalane kopie zapasowe z klientów natywnych SAP HANA (Studio / Cockpit / DBA Cockpit) również nie pojawiają się tutaj.

![Lista zadań tworzenia kopii zapasowych](./media/sap-hana-db-manage/backup-jobs-list.png)

Aby dowiedzieć się więcej na temat monitorowania, przejdź do [monitorowania w witrynie Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) i monitorowania przy użyciu usługi Azure [Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Alerty są łatwym sposobem monitorowania kopii zapasowych baz danych SAP HANA. Alerty pomagają skupić się na zdarzeniach, na których najbardziej Ci zależy, nie gubiąc się w wielu zdarzeniach generowanych przez kopię zapasową. Usługa Azure Backup umożliwia ustawianie alertów i można je monitorować w następujący sposób:

* Zaloguj się do [Portalu Azure](https://portal.azure.com/).
* Na pulpicie nawigacyjnym przechowalni wybierz pozycję **Alerty kopii zapasowych**.

  ![Alerty dotyczące tworzenia kopii zapasowych na pulpicie nawigacyjnym przechowalni](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Alerty będą widoczne:

  ![Lista alertów dotyczących tworzenia kopii zapasowych](./media/sap-hana-db-manage/backup-alerts-list.png)

* Kliknij alerty, aby zobaczyć więcej szczegółów:

  ![Szczegóły alertu](./media/sap-hana-db-manage/alert-details.png)

Obecnie usługa Azure Backup umożliwia wysyłanie alertów za pośrednictwem poczty e-mail. Te alerty są następujące:

* Wyzwalane dla wszystkich błędów kopii zapasowej.
* Skonsolidowane na poziomie bazy danych przez kod błędu.
* Wysyłane tylko w przypadku pierwszego błędu kopii zapasowej bazy danych.

Aby dowiedzieć się więcej na temat monitorowania, przejdź do [monitorowania w witrynie Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) i monitorowania przy użyciu usługi Azure [Monitor.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

## <a name="management-operations"></a>Operacje zarządzania

Usługa Azure Backup ułatwia zarządzanie bazą danych SAP HANA, która jest łatwa dzięki szerokiej liczebności operacji zarządzania, które obsługuje. Operacje te zostały omówione bardziej szczegółowo w poniższych sekcjach.

### <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu przechowalni kliknij polecenie **Zapasy elementów**.
2. W **obszarze Elementy kopii zapasowej**wybierz maszynę wirtualną z uruchomieniem bazy danych SAP HANA, a następnie kliknij przycisk Kopia **zapasowa teraz**.
3. W **obszarze Kopia zapasowa teraz**użyj formantu kalendarza, aby wybrać ostatni dzień, w którym punkt odzyskiwania powinien zostać zachowany. Następnie kliknij przycisk **OK**.
4. Monitorowanie powiadomień portalu. Postęp zadania na pulpicie nawigacyjnym magazynu można monitorować > **Zadania** > kopii zapasowej**w toku**. W zależności od rozmiaru bazy danych utworzenie początkowej kopii zapasowej może chwilę potrwać.

### <a name="hana-native-client-integration"></a>Integracja klienta macierzystego HANA

Teraz pełne kopie zapasowe na żądanie wyzwalane z dowolnego klienta macierzystego HANA pojawią się jako pełna kopia zapasowa na stronie **Elementy kopii zapasowej.**

![Uruchomić ostatnie kopie zapasowe](./media/sap-hana-db-manage/last-backups.png)

Te pełne kopie zapasowe ad hoc pojawią się również na liście punktów przywracania do przywrócenia.

![Lista punktów przywracania](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie natywnej kopii zapasowej klienta SAP HANA w bazie danych z włączoną obsługą kopii zapasowej platformy Azure

Jeśli chcesz wykonać lokalną kopię zapasową (przy użyciu HANA Studio / Cockpit) bazy danych, której kopię zapasową jest kopia zapasowa usługi Azure Backup, wykonaj następujące czynności:

1. Poczekaj na pełne lub dziennikowe kopie zapasowe dla bazy danych, aby zakończyć. Sprawdź stan w SAP HANA Studio/ Cockpit.
2. Wyłącz kopie zapasowe dziennika i ustaw katalog kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie opcję **Systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Ustaw **enable_auto_log_backup** na **Nie**.
5. Ustaw **log_backup_using_backint** na **False**.
6. Wykonaj pełną kopię zapasową bazy danych na żądanie.
7. Poczekaj na zakończenie pełnej kopii zapasowej i katalogu.
8. Przywróć poprzednie ustawienia do tych dla platformy Azure:
   * Ustaw **enable_auto_log_backup** **tak**.
   * Ustaw **log_backup_using_backint** **true**.

### <a name="change-policy"></a>Zmienianie zasad

Można zmienić podstawowe zasady dla elementu kopii zapasowej SAP HANA.

* Na pulpicie nawigacyjnym przechowalni przejdź do **pozycji Zapasy:**

  ![Wybieranie elementów kopii zapasowej](./media/sap-hana-db-manage/backup-items.png)

* Wybieranie **sap hana w usłudze Azure VM**

  ![Wybieranie sap hana w usłudze Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Wybierz element kopii zapasowej, którego podstawowe zasady chcesz zmienić
* Kliknij na istniejące zasady tworzenia kopii zapasowych

  ![Wybieranie istniejących zasad tworzenia kopii zapasowych](./media/sap-hana-db-manage/existing-backup-policy.png)

* Zmień zasady, wybierając z listy. W razie potrzeby [utwórz nową zasadę tworzenia kopii zapasowych.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)

  ![Wybieranie zasad z listy rozwijanej](./media/sap-hana-db-manage/choose-backup-policy.png)

* Zapisywanie zmian

  ![Zapisywanie zmian](./media/sap-hana-db-manage/save-changes.png)

* Modyfikacja zasad wpłynie na wszystkie skojarzone elementy kopii zapasowej i wyzwoli odpowiednie zadania **ochrony konfiguracji.**

>[!NOTE]
> Wszelkie zmiany w okresie przechowywania będą stosowane retrospektywnie do wszystkich starszych punktów odzyskiwania oprócz nowych.
>
> Przyrostowe zasady tworzenia kopii zapasowych nie mogą być używane dla baz danych SAP HANA. Przyrostowa kopia zapasowa nie jest obecnie obsługiwana dla tych baz danych.

### <a name="modify-policy"></a>Modyfikowanie zasad

Modyfikuj zasady, aby zmienić typy kopii zapasowych, częstotliwości i zakres przechowywania.

>[!NOTE]
>Wszelkie zmiany w okresie przechowywania będą stosowane z mocą wsteczną do wszystkich starszych punktów odzyskiwania, oprócz nowych.

1. Na pulpicie nawigacyjnym przechowalni przejdź do **strony Zarządzanie > zasady tworzenia kopii zapasowych** i wybierz zasady, które chcesz edytować.

   ![Wybieranie zasad do edycji](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Wybierz **pozycję Modyfikuj**.

   ![Wybierz pozycję Modyfikuj](./media/sap-hana-db-manage/modify-policy.png)

1. Wybierz częstotliwość dla typów kopii zapasowych.

   ![Wybieranie częstotliwości tworzenia kopii zapasowych](./media/sap-hana-db-manage/choose-frequency.png)

Modyfikacja zasad wpłynie na wszystkie skojarzone elementy kopii zapasowej i wyzwoli odpowiednie zadania **ochrony konfiguracji.**

### <a name="inconsistent-policy"></a>Niespójne zasady

Od czasu do czasu operacja modyfikowania zasad może prowadzić do **niespójnej** wersji zasad dla niektórych elementów kopii zapasowej. Dzieje się tak, gdy odpowiednie zadanie **ochrony konfiguracji** kończy się niepowodzeniem dla elementu kopii zapasowej po wyzwoleniu operacji modyfikowania zasad. W widoku elementu kopii zapasowej jest wyświetlany następujący sposób:

![Niespójne zasady](./media/sap-hana-db-manage/inconsistent-policy.png)

Wersję zasad dla wszystkich elementów, których dotyczy problem, można naprawić za pomocą jednego kliknięcia:

![Napraw wersję zasad](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Ochrona zatrzymania bazy danych SAP HANA

Możesz przestać chronić bazę danych SAP HANA na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się pozostawić punkty odzyskiwania, pamiętaj o tych szczegółach:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone na zawsze, wszystkie przycinanie zatrzymuje się na ochronie przystanku z zachowaniem danych.
* Zostanie naliczona opłata za chronione wystąpienie i zużyte miejsce do magazynowania. Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe nie powiodą się.

Aby zatrzymać ochronę bazy danych:

* Na pulpicie nawigacyjnym przechowalni wybierz pozycję **Zapas elementów**.
* W obszarze **Typ zarządzania kopiami zapasowymi**wybierz **SAP HANA na maszynie Wirtualnej platformy Azure**

  ![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Wybierz bazę danych, dla której chcesz zatrzymać ochronę na:

  ![Wybierz bazę danych, aby zatrzymać ochronę](./media/sap-hana-db-manage/select-database.png)

* W menu bazy danych wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej**.

  ![Wybierz zatrzymaj kopię zapasową](./media/sap-hana-db-manage/stop-backup.png)

* W menu **Zatrzymaj kopię zapasową** wybierz, czy dane mają być zachowywane, czy usuwane. Jeśli chcesz, podaj powód i komentarz.

  ![Wybieranie zachować lub usunąć dane](./media/sap-hana-db-manage/retain-backup-data.png)

* Wybierz **pozycję Zatrzymaj tworzenie kopii zapasowej**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Wznawianie ochrony bazy danych SAP HANA

Po zatrzymaniu ochrony dla bazy danych SAP HANA, jeśli wybierzesz opcję **Zachowaj dane kopii zapasowej,** możesz później wznowić ochronę. Jeśli nie zachowasz kopii zapasowej danych, nie będzie można wznowić ochrony.

Aby wznowić ochronę bazy danych SAP HANA:

* Otwórz element kopii zapasowej i wybierz pozycję **Wznów kopię zapasową**.

   ![Wybierz wznawianie kopii zapasowej](./media/sap-hana-db-manage/resume-backup.png)

* W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Aktualizacja z SAP HANA 1.0 do 2.0

Dowiedz się, jak kontynuować tworzenie kopii zapasowych bazy danych SAP HANA [po uaktualnieniu z SAP HANA 1.0 do 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Uaktualnianie bez zmiany identyfikatora SID

Dowiedz się, jak kontynuować tworzenie kopii zapasowych bazy danych SAP HANA, której [identyfikator SID nie uległ zmianie po uaktualnieniu.](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)

### <a name="unregister-an-sap-hana-instance"></a>Wyrejestrować wystąpienie SAP HANA

Wyrejestruj wystąpienie SAP HANA po wyłączeniu ochrony, ale przed usunięciem przechowalni:

* Na pulpicie nawigacyjnym przechowalni w obszarze **Zarządzaj**wybierz pozycję **Infrastruktura kopii zapasowych**.

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/sap-hana-db-manage/backup-infrastructure.png)

* Wybierz **typ zarządzania kopiami zapasowymi** jako **obciążenie maszyny Wirtualnej platformy Azure**

   ![Wybierz typ zarządzania kopiami zapasowymi jako obciążenie maszyny Wirtualnej platformy Azure](./media/sap-hana-db-manage/backup-management-type.png)

* W **obszarze Serwery chronione**wybierz wystąpienie do wyrejestrowania. Aby usunąć przechowalnię, należy wyrejestrować wszystkie serwery/ wystąpienia.

* Kliknij prawym przyciskiem myszy chronione wystąpienie i wybierz polecenie **Wyrejestruj**.

   ![Wybierz pozycję wyrejestrować](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Ponowne zarejestrowanie rozszerzenia na maszynie wirtualnej serwera SAP HANA

Czasami rozszerzenie obciążenia na maszynie wirtualnej może mieć wpływ z tego czy innego powodu. W takich przypadkach wszystkie operacje wyzwalane na maszynie Wirtualnej rozpocznie się niepowodzeniem. Następnie może być konieczne ponowne zarejestrowanie rozszerzenia na maszynie Wirtualnej. Ponowne rejestrowanie operacji ponownie instaluje rozszerzenie kopii zapasowej obciążenia na maszynie Wirtualnej, aby operacje były kontynuowane.

Użyj tej opcji ostrożnie: po wyzwoleniu na maszynie Wirtualnej z już zdrowym rozszerzeniem ta operacja spowoduje ponowne uruchomienie rozszerzenia. Może to spowodować, że wszystkie zadania w toku zakończy się niepowodzeniem. Sprawdź, czy co najmniej jeden z [objawów](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) przed wyzwoleniem operacji ponownego rejestrowania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowej baz danych SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
