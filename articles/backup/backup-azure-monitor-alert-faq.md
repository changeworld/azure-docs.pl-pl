---
title: Usługa Azure Backup monitorowania alertów — często zadawane pytania
description: 'Odpowiedzi na często zadawane pytania dotyczące: Alert monitorowania kopii zapasowych platformy Azure'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: bb684f65539b4429862b2dce0e378d8f659d2975
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705041"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Usługa Azure Backup monitorowania Alert — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące platformy Azure alert monitorowania.

## <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Jak sprawdzić, czy dane raportowania zostało przepływać do konta magazynu?
Przejdź do konta magazynu, które zostały skonfigurowane i wybierz kontenery. Jeśli kontener zawiera wpis dla azurebackupreport — dzienniki — szczegółowe informacje, oznacza to, dane raportowania uruchomienia przepływu.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Co to jest częstotliwość wypychanie danych do konta magazynu i pakietowi zawartości usługi Azure Backup w usłudze Power BI?
  Dla użytkowników w dniu 0 trwa około 24 godzinach wypychanie danych do konta magazynu. Po zakończeniu tej początkowej wypychane dane są odświeżane z częstotliwością pokazano na poniższej ilustracji.

  * Dane powiązane z **zadania**, **alerty**, **elementy kopii zapasowej**, **magazynów**, **serwerów chronionych**i  **Zasady** zostanie przypisany do konta magazynu klienta, jak i po jego zalogowaniu.

  * Dane powiązane z **magazynu** zostanie przypisany do konta magazynu klienta co 24 godziny.

       ![Częstotliwość wypychania danych w usłudze Azure raporty kopii zapasowych](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Usługa Power BI ma [zaplanowanego odświeżania raz dziennie](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Można przeprowadzić ręczne odświeżanie danych w usłudze Power BI dla pakietu zawartości.

### <a name="how-long-can-i-retain-reports"></a>Jak długo można zachować raportów
Po skonfigurowaniu konta magazynu, można wybrać okres przechowywania danych raportu na koncie magazynu. Postępuj zgodnie z kroku 6 [Konfigurowanie konta magazynu dla raportów](backup-azure-configure-reports.md#configure-storage-account-for-reports) sekcji. Możesz również [analizowanie raportów w programie Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) i zapisać je na dłuższy okres przechowywania, zgodnie z potrzebami.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Moje dane w raportach będą widoczne po skonfigurować konto magazynu?
 Wszystkie dane, które są generowane po skonfigurowaniu konta magazynu zostanie przypisany do konta magazynu i jest dostępna w raportach. W toku zadania nie są wypychane do raportowania. Gdy zadanie zakończy się lub kończy się niepowodzeniem, jest wysyłana do raportów.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Jeśli mam już skonfigurowane konto magazynu, aby wyświetlić raporty, można zmienić konfigurację, aby użyć innego konta magazynu?
Tak, można zmienić konfiguracji, aby wskazywał innego konta magazynu. Podczas łączenia z pakietem zawartości usługi Azure Backup, należy użyć nowo skonfigurowanego konta magazynu. Ponadto po skonfigurowaniu konta magazynu innego nowego przepływu danych na tym koncie magazynu. Starsze dane (przed zmianą konfiguracji) nadal pozostaje w starszych konta magazynu.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>W subskrypcji i magazynów mogą wyświetlać raporty?
Tak, można skonfigurować tego samego konta magazynu w różnych magazynów, aby wyświetlić raporty dla wielu magazynu. Ponadto można skonfigurować tego samego konta magazynu dla magazynów w subskrypcjach. Następnie przy użyciu tego konta magazynu, gdy połączysz się z pakietem zawartości usługi Azure Backup w usłudze Power BI do wyświetlania raportów. Wybrane konto magazynu musi być w tym samym regionie co magazyn usługi Recovery Services.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa aż stan zadania agenta usługi Azure backup to odzwierciedlone w portalu?
Witryny Azure portal może potrwać do 15 minut podsumowująca stan zadania agenta usługi Azure backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Gdy zadanie tworzenia kopii zapasowej nie powiedzie się, jak długo trwa wywołania alertu?
W ciągu 20 minutach niepowodzenia wykonywania kopii zapasowej platformy Azure zostanie zgłoszony alert.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Jest przypadek, w których nie będzie wysyłana wiadomość e-mail że powiadomienia zostały skonfigurowane?
Tak. W następujących sytuacjach nie są wysyłane powiadomienia.

* Jeśli powiadomienia zostały skonfigurowane co godzinę, a alert jest wyświetlany i rozwiązywany w ciągu godziny
* Kiedy zadanie zostanie anulowane
* Jeśli drugie zadanie tworzenia kopii zapasowej nie powiedzie się, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa aż stan zadania agenta usługi Azure backup to odzwierciedlone w portalu?
Witryny Azure portal może potrwać do 15 minut podsumowująca stan zadania agenta usługi Azure backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Gdy zadanie tworzenia kopii zapasowej nie powiedzie się, jak długo trwa wywołania alertu?
W ciągu 20 minutach niepowodzenia wykonywania kopii zapasowej platformy Azure zostanie zgłoszony alert.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Jest przypadek, w których nie będzie wysyłana wiadomość e-mail że powiadomienia zostały skonfigurowane?
Tak. W następujących sytuacjach nie są wysyłane powiadomienia.

* Jeśli powiadomienia zostały skonfigurowane co godzinę, a alert jest wyświetlany i rozwiązywany w ciągu godziny
* Kiedy zadanie zostanie anulowane
* Jeśli drugie zadanie tworzenia kopii zapasowej nie powiedzie się, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="next-steps"></a>Następne kroki

Przeczytaj inne — często zadawane pytania:

- [Często zadawane pytania dotyczące](backup-azure-vm-backup-faq.md) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure.
- [Często zadawane pytania dotyczące](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
