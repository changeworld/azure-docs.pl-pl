---
title: Tworzenie klasycznej alertów dla usług Azure za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Wyzwalanie powiadomienia lub wiadomości e-mail, lub zadzwoń adresów URL witryny sieci Web (elementy webhook) lub automatyzacji, po spełnieniu warunków, które określisz.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287433"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Tworzenie klasycznej metryki alertów w monitorze Azure dla usług Azure za pomocą portalu Azure 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [Program PowerShell](insights-alerts-powershell.md)
> * [Interfejs wiersza polecenia](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alerty metryki. Azure obsługuje teraz Monitor [nowszej alerty metryki](monitoring-near-real-time-metric-alerts.md). 


W tym artykule przedstawiono sposób konfigurowania klasycznego Azure alerty metryki przy użyciu portalu Azure. 

Otrzymasz alert w oparciu metryki dla usługi Azure, lub możesz otrzymywać alerty dla zdarzenia występujące w systemie Azure.

* **Wartości metryki**: uruchamia alert, gdy wartość określonej metryki przekracza próg, który przypisywać w żadnym kierunku. Oznacza to, że oba wyzwala po raz pierwszy warunek jest spełniony, a następnie po już trwa spełnienia warunku.    

* **Zdarzenia dziennika aktywności**: alert może wyzwolić na *co* zdarzenia lub w przypadku wystąpienia określonych zdarzeń. Dowiedz się więcej o [alertów dotyczących działań w dzienniku](monitoring-activity-log-alerts.md).

Można skonfigurować klasycznego alertu metryki wyzwala, wykonaj następujące czynności:

* Administrator usługi i współadministratorzy wysyłania powiadomień e-mail.
* Wyślij wiadomość e-mail do określonych adresów e-mail dodatkowych.
* Wywołanie elementu webhook.
* Uruchamia wykonywanie elementów runbook platformy Azure (tylko z portalu Azure).

Można skonfigurować i uzyskać informacje na temat klasycznego metryki reguły alertów z następujących lokalizacji: 

* [Azure Portal](insights-alerts-portal.md)
* [Program PowerShell](insights-alerts-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](insights-alerts-command-line-interface.md)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertu na Metryka z portalu Azure
1. W [portal](https://portal.azure.com/), zlokalizuj zasób, który ma być monitorowany, a następnie wybierz go.

2. W **monitorowanie** zaznacz **alertów (klasyczne)**. Tekst i ikona mogą się nieco różnić dla różnych zasobów. Jeśli nie znajdziesz **alertów (klasyczne)** , może być w **alerty** lub **reguły alertów**.

    ![Monitorowanie](./media/insights-alerts-portal/AlertRulesButton.png)

3. Wybierz **Dodaj metryki alert (klasyczne)** polecenia, a następnie wypełnij pola.

    ![Dodaj alert](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nazwa** regułę alertu. Następnie wybierz pozycję **opis**, który jest także wyświetlany w wiadomości e-mail z powiadomieniem.

5. Wybierz **Metryka** , który chcesz monitorować. Następnie wybierz pozycję **warunku** i **próg** wartość metryki. Wybierz również **okres** czas, przez który metryki reguły muszą zostać spełnione przed wyzwalaczy alertu. Na przykład jeśli używasz okresu "w ciągu ostatnich 5 minut" i alertu szuka procesora CPU przekracza 80%, alert jest wyzwalane po procesora CPU było stale powyżej 80% 5 minut. Po pierwszym wyzwalacza występuje, ponownie wyzwala podczas Procesora pozostaje poniżej 80% 5 minut. Metryki pomiaru Procesora odbywa się co minutę.

6. Wybierz **E-mail właścicieli...**  Jeśli chcesz, aby administratorzy i współadministratorzy, aby otrzymywać powiadomienia pocztą e-mail po zgłoszeniu alertu.

7. Jeśli chcesz wysłać powiadomienia na adresy e-mail dodatkowych po zgłoszeniu alertu, dodaj je w **email(s) dodatkowe administratora** pola. Wiele wiadomości e-mail należy rozdzielić średnikami w następującym formacie:  *email@contoso.com; email2@contoso.com*

8. Umieść w prawidłowym identyfikatorem URI w **Webhook** pola, jeśli ma być wywoływana po zgłoszeniu alertu.

9. Jeśli używasz usługi Automatyzacja Azure, możesz wybrać element runbook do uruchomienia po zgłoszeniu alertu.

10. Wybierz **OK** można utworzyć alertu.   

W ciągu kilku minut alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu, można ją zaznaczyć i wykonaj jedną z następujących czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywistymi wartościami z poprzedniego dnia.
* Edytuj lub usuń go.
* **Wyłącz** lub **włączyć** go, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień dla tego alertu.

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie monitorowania Azure](monitoring-overview.md), w tym typy informacji, można zbierać i monitorowania.
* Dowiedz się więcej o [nowszej alerty metryki](monitoring-near-real-time-metric-alerts.md).
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](insights-webhooks-alerts.md).
* Dowiedz się więcej o [konfigurowania alertów na zdarzenia dziennika aktywności](monitoring-activity-log-alerts.md).
* Dowiedz się więcej o [elementu runbook usługi Automatyzacja Azure](../automation/automation-starting-a-runbook.md).
* Pobierz [Przegląd dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md)i zbieranie szczegółowych metryki wysokiej częstotliwości w usłudze.
* Pobierz [omówienie zbierania metryk](insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
