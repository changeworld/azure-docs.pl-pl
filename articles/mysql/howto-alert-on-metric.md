---
title: Konfigurowanie alertów metryk dla usługi Azure Database for MySQL w witrynie Azure portal
description: Tym artykule opisano sposób konfigurowania i alertów dotyczących metryk dostępu dla usługi Azure Database for MySQL w witrynie Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 999b1d03ad8cb0b27de10ff6457c0e6cc9112ee7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548735"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Konfigurowanie alertów dotyczących metryk usługi Azure Database for MySQL za pomocą witryny Azure portal 

W tym artykule przedstawiono sposób konfigurowania bazy danych Azure MySQL alertów w witrynie Azure portal. Otrzymasz alert na podstawie monitorowania metryk usług platformy Azure.

Alertu powoduje wyzwolenie, gdy wartość określonej metryki przekracza wartość progową, w których zostanie przypisany. Alert wyzwalacze zarówno, gdy warunek zostanie spełniony, a następnie później podczas warunku nie jest już trwa spełniany. 

Można skonfigurować alert, aby po jego wyzwoleniu, wykonaj następujące czynności:
* wysyłanie powiadomień e-mail do administratora usługi i współadministratorzy
* Wyślij wiadomość e-mail do dodatkowe adresy e-mail, które określisz.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje na temat reguł alertów za pomocą:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Program PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs wiersza polecenia (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu na metryki w witrynie Azure portal
1. W [witryny Azure portal](https://portal.azure.com/), wybierz bazę danych Azure dla serwera MySQL, którą chcesz monitorować.

2. W obszarze **monitorowanie** części paska bocznego wybierz **reguły alertów** jak pokazano:

   ![Wybierz reguły alertów](./media/howto-alert-on-metric/1-alert-rules.png)

3. Wybierz **Dodaj alert dotyczący metryki** (ikony +). 

4. **Dodaj regułę** zostanie otwarta strona, jak pokazano poniżej.  Wprowadź wymagane informacje:

   ![Dodaj formularz alertu metryki](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Ustawienie | Opis  |
   |---------|---------|
   | Name (Nazwa) | Podaj nazwę reguły alertu. Ta wartość jest wysyłana w wiadomości e-mail powiadomień o alertach. |
   | Opis | Podaj krótki opis reguły alertu. Ta wartość jest wysyłana w wiadomości e-mail powiadomień o alertach. |
   | Alert dla | Wybierz **metryki** dla tego rodzaju alert. |
   | Subskrypcja | To pole jest wstępnie wypełnione przy użyciu subskrypcji, który jest hostem usługi Azure Database for MySQL. |
   | Grupa zasobów | To pole jest wstępnie wypełnione przy użyciu grupy zasobów usługi Azure Database for MySQL. |
   | Zasób | To pole jest wypełniana wstępnie o nazwie usługi Azure Database for MySQL. |
   | Metryka | Wybierz metrykę, którą chcesz wystawić alert w przypadku. Na przykład **procent użycia magazynu**. |
   | Warunek | Wybierz warunek dla metryki, które ma zostać porównane z. Na przykład **większa**. |
   | Próg | Wartość progowa metryki, na przykład 85 (procent). |
   | Okres | Okres czasu, przez jaki reguła metryki muszą być spełnione przed wyzwala alert. Na przykład **w ciągu ostatnich 30 minut**. |

   Na podstawie przykładu, alert szuka procent użycia magazynu powyżej 85% w okresie 30 minut. Wyzwala alert w przypadku średnią wartość procentową magazynu został powyżej 85% w ciągu 30 minut. Gdy wystąpi to pierwszy wyzwalacz, wyzwala ponownie średnią wartość procentową magazynu po poniżej 85% ponad 30 minut.

5. Wybierz metodę powiadomienia dla reguły alertu. 

   Sprawdź **właściciele, współautorzy i czytelnicy poczty E-mail** opcję, jeśli chcesz, aby administratorzy subskrypcji i współadministratorzy w celu przesłania pocztą e-mail, gdy zostanie wyzwolony alert.

   Dodatkowe wiadomości e-mail, aby otrzymać powiadomienie, gdy zostanie wyzwolony alert, dodać je w **email(s) dodatkowego administratora** pola. Wiele wiadomości e-mail należy rozdzielić średnikami -  *email@contoso.com;email2@contoso.com*

   Opcjonalnie możesz podać prawidłowy identyfikator URI w **elementu Webhook** jeśli ma ona wywoływana, gdy zostanie wyzwolony alert.

6. Wybierz pozycję **OK**, aby utworzyć alert.

   W ciągu kilku minut ten alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu, można ją zaznaczyć i wykonaj następujące czynności:

* Wyświetl wykres przedstawiający próg metryki i rzeczywiste wartości z poprzedniego dnia związanych z tym alertem.
* **Edytuj** lub **Usuń** reguły alertu.
* **Wyłącz** lub **Włącz** alert, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [konfigurowania elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Pobierz [omówienie zbierania metryk](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i działa prawidłowo.
