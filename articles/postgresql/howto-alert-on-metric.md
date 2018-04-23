---
title: Konfigurowanie alertów metryki dla bazy danych Azure w programie PostgreSQL w portalu Azure
description: W tym artykule opisano sposób konfigurowania i uzyskiwanie dostępu do alertów metryki dla bazy danych Azure dla PostgreSQL z portalu Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b4b15998276dd6c32e9c15622aa0251c6c066085
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Konfigurowanie alertów dla metryki dla bazy danych Azure dla PostgreSQL za pomocą portalu Azure 

W tym artykule przedstawiono sposób konfigurowania bazy danych Azure, PostgreSQL alertów za pomocą portalu Azure. Możesz otrzymywać alertu opartego na monitorowanie metryki dla usług Azure.

Wyzwalaczy alertu, gdy wartość określonej metryki przekracza wartość progową, które można przypisać. Alert wyzwalacze zarówno, gdy warunek zostanie spełniony, a następnie później podczas warunku jest już spełniane. 

Można skonfigurować alert o konieczności wyzwala, wykonaj następujące czynności:
* Administrator usługi i współadministratorzy wysyłania powiadomień e-mail.
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail przez użytkownika.
* Wywołanie elementu webhook.

Można skonfigurować i uzyskać informacje na temat reguł alertów za pomocą:
* [Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Program PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interfejs wiersza polecenia (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu na Metryka z portalu Azure
1. W [portalu Azure](https://portal.azure.com/), wybierz bazę danych Azure PostgreSQL serwera, który chcesz monitorować.

2. W obszarze **monitorowanie** części paska bocznego, wybierz opcję **reguły alertów** pokazany:

   ![Wybierz reguły alertu](./media/howto-alert-on-metric/1-alert-rules.png)

3. Wybierz **Dodaj alert metryki** (+ ikony). 

4. **Dodaj regułę** zostanie otwarta strona, jak pokazano poniżej.  Wprowadź wymagane informacje:

   ![Dodawanie metryki formularza alertu](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Ustawienie | Opis  |
   |---------|---------|
   | Name (Nazwa) | Podaj nazwę reguły alertów. Ta wartość jest wysyłane pocztą e-mail powiadomienia o alertach. |
   | Opis | Podaj krótki opis reguły alertu. Ta wartość jest wysyłane pocztą e-mail powiadomienia o alertach. |
   | Alert dla | Wybierz **metryki** dla tego typu alertu. |
   | Subskrypcja | To pole jest wstępnie wypełnione subskrypcji, który jest hostem bazy danych Azure dla PostgreSQL. |
   | Grupa zasobów | To pole jest wypełniana wstępnie z grupą zasobów bazy danych Azure dla PostgreSQL. |
   | Zasób | To pole jest wypełniana wstępnie o nazwie bazy danych Azure dla PostgreSQL. |
   | Metryka | Wybierz metrykę, która ma być alert w przypadku. Na przykład **procent użycia magazynu**. |
   | Warunek | Wybierz warunek, który ma zostać porównane z metryki. Na przykład **większa niż**. |
   | Próg | Wartość progowa metryki, na przykład 85 (w procentach). |
   | Okres | Okres czasu, przez który metryki reguły muszą zostać spełnione przed wyzwalaczy alertu. Na przykład **w ciągu ostatnich 30 minut**. |

   Na podstawie przykładu, alert szuka procent użycia magazynu powyżej 85% przez okres do 30 minut. Ten alert jest wyzwalane po średnią wartość procentową magazynu został powyżej 85% do 30 minut. W momencie to pierwszy wyzwalacz wyzwoli ponownie średnią wartość procentową magazynu po poniżej 85% ponad 30 minut.

5. Wybierz metodę powiadomień dla reguły alertów. 

   Sprawdź **E-mail właściciele, współautorzy i czytelnicy** opcję, jeśli chcesz, aby administratorzy subskrypcji i współadministratorzy w celu przesłania pocztą e-mail po zgłoszeniu alertu.

   Jeśli chcesz, dodatkowe wiadomości e-mail, aby otrzymać powiadomienie po zgłoszeniu alertu, dodaj je w **email(s) dodatkowe administratora** pola. Wiele wiadomości e-mail należy rozdzielić średnikami -  *email@contoso.com;email2@contoso.com*

   Opcjonalnie, podaj prawidłowy identyfikator URI w **Webhook** jeśli ma ona wywoływana po zgłoszeniu alertu.

6. Wybierz **OK** można utworzyć alertu.

   W ciągu kilku minut alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu można ją zaznaczyć i wykonywanie następujących czynności:

* Wyświetl wykres przedstawiający próg metryki i rzeczywistymi wartościami z poprzedniego dnia dotyczy ten alert.
* **Edytuj** lub **usunąć** reguły alertów.
* **Wyłącz** lub **włączyć** alert, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Pobierz [omówienie zbierania metryk](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
