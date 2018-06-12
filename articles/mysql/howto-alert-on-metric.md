---
title: Konfigurowanie alertów metryki dla bazy danych Azure dla programu MySQL w portalu Azure
description: W tym artykule opisano sposób konfigurowania i uzyskiwanie dostępu do alertów metryki dla bazy danych Azure dla programu MySQL z portalu Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 3accc31f433e6db40c7d1de2b56dfbd4180b4933
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265187"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Użyj portalu Azure, aby skonfigurować alerty dotyczące metryk bazy danych Azure dla programu MySQL 

W tym artykule przedstawiono sposób konfigurowania bazy danych Azure, MySQL alertów za pomocą portalu Azure. Możesz otrzymywać alertu opartego na monitorowanie metryki dla usług Azure.

Wyzwalaczy alertu, gdy wartość określonej metryki przekracza wartość progową, które można przypisać. Alert wyzwalacze zarówno, gdy warunek zostanie spełniony, a następnie później podczas warunku jest już spełniane. 

Można skonfigurować alert o konieczności wyzwala, wykonaj następujące czynności:
* wysyłanie powiadomień e-mail do administratora usługi i współadministratorzy
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail przez użytkownika.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje na temat reguł alertów za pomocą:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Program PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interfejs wiersza polecenia (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu na Metryka z portalu Azure
1. W [portalu Azure](https://portal.azure.com/), wybierz bazę danych Azure MySQL serwera, który chcesz monitorować.

2. W obszarze **monitorowanie** części paska bocznego, wybierz opcję **reguły alertów** pokazany:

   ![Wybierz Alert, reguły](./media/howto-alert-on-metric/1-alert-rules.png)

3. Wybierz **Dodaj alert metryki** (+ ikony). 

4. **Dodaj regułę** zostanie otwarta strona, jak pokazano poniżej.  Wprowadź wymagane informacje:

   ![Dodawanie metryki formularza alertu](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Ustawienie | Opis  |
   |---------|---------|
   | Name (Nazwa) | Podaj nazwę reguły alertów. Ta wartość jest wysyłane pocztą e-mail powiadomienia o alertach. |
   | Opis | Podaj krótki opis reguły alertu. Ta wartość jest wysyłane pocztą e-mail powiadomienia o alertach. |
   | Alert dla | Wybierz **metryki** dla tego typu alertu. |
   | Subskrypcja | To pole jest wstępnie wypełnione subskrypcji, który jest hostem bazy danych Azure dla programu MySQL. |
   | Grupa zasobów | To pole jest wypełniana wstępnie z grupą zasobów bazy danych Azure dla programu MySQL. |
   | Zasób | To pole jest wypełniana wstępnie o nazwie bazy danych Azure dla programu MySQL. |
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
