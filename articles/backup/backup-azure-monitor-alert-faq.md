---
title: Alert monitorowania Azure Backup — często zadawane pytania
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące alertu monitorowania Azure Backup i raportów Azure Backup.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: dacurwin
ms.openlocfilehash: dc0225092d23371ca97dfedd48a2d3ffcf85a9be
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747398"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alert monitorowania Azure Backup — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące alertu monitorowania platformy Azure.

## <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Jak mogę sprawdzić, czy dane raportowania rozpoczęły przepływ do konta magazynu?

Przejdź do skonfigurowanego konta magazynu i wybierz pozycję kontenery. Jeśli kontener zawiera wpis Insights-Logs-azurebackupreport, wskazuje, że dane raportowania zostały uruchomione w.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Jaka jest częstotliwość wypychania danych do konta magazynu i pakietu zawartości Azure Backup w Power BI?

  W przypadku użytkowników dnia 0, wypychanie danych do konta magazynu trwa około 24 godzin. Po zakończeniu początkowej wypychania dane są odświeżane przy częstotliwości pokazanej na poniższej ilustracji.

* Dane dotyczące **zadań**, **alertów**, **elementów kopii zapasowych**, **magazynów**, **serwerów chronionych**i **zasad** są wypychane do konta magazynu klienta, tak jak i po jego zarejestrowaniu.

* Dane związane z **magazynem** są przekazywane do konta magazynu klienta co 24 godziny.

    ![Częstotliwość wypychania danych raportów Azure Backup](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI ma [zaplanowane odświeżanie raz dziennie](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Możesz wykonać ręczne odświeżanie danych w Power BI dla pakietu zawartości.

### <a name="how-long-can-i-retain-reports"></a>Jak długo można przechowywać raporty?

Podczas konfigurowania konta magazynu można wybrać okres przechowywania danych raportu na koncie magazynu. Wykonaj krok 6 w sekcji [Konfigurowanie konta magazynu dla raportów](backup-azure-configure-reports.md#configure-storage-account-for-reports) . Możesz również [analizować raporty w programie Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) i zapisywać je na dłuższy okres przechowywania na podstawie Twoich potrzeb.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Czy po skonfigurowaniu konta magazynu zobaczysz wszystkie dane w raportach?

 Wszystkie dane wygenerowane po skonfigurowaniu konta magazynu są przekazywane do konta magazynu i dostępne w raportach. Zadania w toku nie są wypychane na potrzeby raportowania. Po zakończeniu zadania lub niepowodzeniu zostanie ono wysłane do raportów.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Czy jeśli konto magazynu zostało już skonfigurowane do wyświetlania raportów, czy mogę zmienić konfigurację tak, aby korzystała z innego konta magazynu?

Tak, możesz zmienić konfigurację tak, aby wskazywała na inne konto magazynu. Po nawiązaniu połączenia z pakietem zawartości Azure Backup Użyj nowo skonfigurowanego konta magazynu. Ponadto po skonfigurowaniu innego konta magazynu Nowe przepływy danych na tym koncie magazynu. Starsze dane (przed zmianą konfiguracji) nadal pozostają na starszym koncie magazynu.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Czy mogę wyświetlać raporty w magazynach i subskrypcjach?

Tak, można skonfigurować to samo konto magazynu w różnych magazynach w celu wyświetlenia raportów między magazynami. Ponadto można skonfigurować to samo konto magazynu dla magazynów w różnych subskrypcjach. Następnie można użyć tego konta magazynu podczas łączenia się z pakietem zawartości Azure Backup w Power BI, aby wyświetlić raporty. Wybrane konto magazynu musi znajdować się w tym samym regionie co magazyn Recovery Services.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa stan zadania agenta usługi Azure Backup na potrzeby odzwierciedlenia w portalu?

Aby odzwierciedlić stan zadania agenta usługi Azure Backup, Azure Portal może potrwać do 15 minut.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Kiedy zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, jak długo trwa zgłaszanie alertu?

Alert jest zgłaszany w ciągu 20 minut awarii usługi Azure Backup.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Czy istnieje przypadek, w którym nie będzie wysyłana wiadomość e-mail, jeśli są skonfigurowane powiadomienia?

Tak. W poniższych sytuacjach powiadomienia nie są wysyłane.

* Jeśli powiadomienia są konfigurowane co godzinę, a alert zostanie zgłoszony i rozwiązany w ciągu godziny
* Gdy zadanie zostało anulowane
* Jeśli drugie zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="recovery-services-vault"></a>Magazyn Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa stan zadania agenta usługi Azure Backup na potrzeby odzwierciedlenia w portalu?

Aby odzwierciedlić stan zadania agenta usługi Azure Backup, Azure Portal może potrwać do 15 minut.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Kiedy zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, jak długo trwa zgłaszanie alertu?

Alert jest zgłaszany w ciągu 20 minut awarii usługi Azure Backup.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Czy istnieje przypadek, w którym nie będzie wysyłana wiadomość e-mail, jeśli są skonfigurowane powiadomienia?

Tak. W następujących sytuacjach powiadomienia nie są wysyłane:

* Jeśli powiadomienia są konfigurowane co godzinę, a alert zostanie zgłoszony i rozwiązany w ciągu godziny
* Gdy zadanie zostało anulowane
* Jeśli drugie zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi NZP:

* [Często zadawane pytania](backup-azure-vm-backup-faq.md) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure.
* [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta Azure Backup
