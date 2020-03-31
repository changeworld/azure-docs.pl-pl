---
title: Monitorowanie alertów i raportów często zadawane pytania
description: W tym artykule odnajduj odpowiedzi na typowe pytania dotyczące alertu monitorowania kopii zapasowych platformy Azure i raportów usługi Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989573"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alert monitorowania kopii zapasowej platformy Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące monitorowania i raportowania usługi Azure Backup.

## <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Jak sprawdzić, czy dane raportowania zaczęły przepływać do obszaru roboczego usługi Log Analytics (LA)?

Przejdź do skonfigurowanego obszaru roboczego LA, przejdź do elementu menu **Dzienniki** i uruchom kwerendę CoreAzureBackup | wziąć 1. Jeśli widzisz zwracany rekord, oznacza to, że dane zaczęły przepływać do obszaru roboczego. Początkowe wypychanie danych może potrwać do 24 godzin.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Jaka jest częstotliwość wypychania danych do obszaru roboczego LA Workspace?

Dane diagnostyczne z przechowalni są pompowane do obszaru roboczego usługi Log Analytics z pewnym opóźnieniem. Każde zdarzenie dociera do obszaru roboczego usługi Log Analytics 20 do 30 minut po wypchnięciu go ze skarbca usług odzyskiwania. Oto dalsze szczegóły dotyczące opóźnienia:

* We wszystkich rozwiązaniach wbudowane alerty usługi tworzenia kopii zapasowych są wypychane natychmiast po ich utworzeniu. Dlatego zwykle pojawiają się w obszarze roboczym usługi Log Analytics po 20 do 30 minut.
* We wszystkich rozwiązaniach zadania tworzenia kopii zapasowych na żądanie i zadania przywracania są wypychane natychmiast po ich zakończeniu.
* Dla wszystkich rozwiązań z wyjątkiem kopii zapasowej SQL zaplanowane zadania tworzenia kopii zapasowych są wypychane natychmiast po ich zakończeniu.
* W przypadku kopii zapasowej SQL, ponieważ kopie zapasowe dziennika mogą występować co 15 minut, informacje o wszystkich ukończonych zaplanowanych zadaniach tworzenia kopii zapasowych, w tym dziennikach, są wsadowe i wypychane co 6 godzin.
* We wszystkich rozwiązaniach inne informacje, takie jak element kopii zapasowej, zasady, punkty odzyskiwania, magazyn i tak dalej, są wypychane co najmniej raz dziennie.
* Zmiana konfiguracji kopii zapasowej (na przykład zmiana zasad lub zasady edycji) wyzwala wypychanie wszystkich powiązanych informacji o kopii zapasowej.

### <a name="how-long-can-i-retain-reporting-data"></a>Jak długo mogę przechowywać dane raportowania?

Po utworzeniu obszaru roboczego LA można zachować dane przez maksymalnie 2 lata. Domyślnie la obszar roboczy zachowuje dane przez 31 dni.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Czy wszystkie moje dane będą widoczne w raportach po skonfigurowaniu obszaru roboczego LA Workspace?

 Wszystkie dane wygenerowane po skonfigurowaniu ustawień diagnostycznych są wypychane do obszaru roboczego LA i są dostępne w raportach. Zadania w toku nie są wypychane do raportowania. Po zakończeniu lub niepowodzenia zadania jest ono wysyłane do raportów.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Czy mogę wyświetlać raporty w przechowalniach i subskrypcjach?

Tak, możesz wyświetlać raporty w przechowalniach i subskrypcjach, a także regionach. Twoje dane mogą znajdować się w jednym obszarze roboczym LA Workspace lub w grupie la workspaces.

### <a name="can-i-view-reports-across-tenants"></a>Czy mogę wyświetlać raporty między dzierżawcami?

Jeśli jesteś użytkownikiem [latarni morskiej platformy Azure](https://azure.microsoft.com/services/azure-lighthouse/) z delegowanym dostępem do subskrypcji klientów lub obszarów roboczych LA, możesz użyć raportów kopii zapasowych, aby wyświetlić dane we wszystkich dzierżawach.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa odbicie stanu zadania agenta kopii zapasowej platformy Azure w portalu?

Witryna Azure portal może potrwać do 15 minut, aby odzwierciedlić stan zadania agenta kopii zapasowej platformy Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Ile czasu trwa wywołanie alertu po niepowodzeniu zadania tworzenia kopii zapasowej?

Alert jest wywoływany w ciągu 20 minut od błędu kopii zapasowej platformy Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Czy istnieje przypadek, w którym wiadomość e-mail nie zostanie wysłana, jeśli skonfigurowano powiadomienia?

Tak. W następujących sytuacjach powiadomienia nie są wysyłane.

* Jeśli powiadomienia są skonfigurowane co godzinę, a alert jest wywoływany i rozpoznawany w ciągu godziny
* Po anulowaniu zadania
* Jeśli drugie zadanie tworzenia kopii zapasowej nie powiedzie się, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa odbicie stanu zadania agenta kopii zapasowej platformy Azure w portalu?

Witryna Azure portal może potrwać do 15 minut, aby odzwierciedlić stan zadania agenta kopii zapasowej platformy Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Ile czasu trwa wywołanie alertu po niepowodzeniu zadania tworzenia kopii zapasowej?

Alert jest wywoływany w ciągu 20 minut od błędu kopii zapasowej platformy Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Czy istnieje przypadek, w którym wiadomość e-mail nie zostanie wysłana, jeśli skonfigurowano powiadomienia?

Tak. W następujących sytuacjach powiadomienia nie są wysyłane:

* Jeśli powiadomienia są skonfigurowane co godzinę, a alert jest wywoływany i rozpoznawany w ciągu godziny
* Po anulowaniu zadania
* Jeśli drugie zadanie tworzenia kopii zapasowej nie powiedzie się, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

* [Często zadawane pytania](backup-azure-vm-backup-faq.md) dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.
* [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
