---
title: Konfigurowanie alertów — witryna Azure portal — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania alertów metryk usługi Azure Database dla usługi PostgreSQL — pojedynczy serwer z witryny Azure portal i uzyskiwania do nich dostępu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fe099dcb49d176d27466c08749a5873904d1ae2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74766841"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Konfigurowanie alertów dotyczących metryk bazy danych usługi Azure Database dla usługi PostgreSQL — single server — za pomocą witryny Azure Portal

W tym artykule pokazano, jak skonfigurować usługę Azure Database dla alertów PostgreSQL przy użyciu witryny Azure portal. Możesz otrzymać alert na podstawie monitorowania metryk dla usług platformy Azure.

Alert wyzwala, gdy wartość określonej metryki przekracza próg, który można przypisać. Alert wyzwala zarówno wtedy, gdy warunek jest po raz pierwszy spełnione, a następnie, gdy warunek ten nie jest już spełniony. 

Alert można skonfigurować tak, aby uruchamiał następujące czynności:
* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów.
* Wyślij wiadomość e-mail na dodatkowe określone wiadomości e-mail.
* Zadzwoń do elementu webhook.

Za pomocą:
* [Portal Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Interfejs API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertów na metryce z witryny Azure portal
1. W [witrynie Azure portal](https://portal.azure.com/)wybierz serwer Usługi Azure Database dla postgreSQL, który chcesz monitorować.

2. W sekcji **Monitorowanie** paska bocznego wybierz **pozycję Alerty,** jak pokazano na rysunku:

   ![Wybierz reguły alertów](./media/howto-alert-on-metric/2-alert-rules.png)

3. Wybierz **pozycję Dodaj alert metryki** (+ ikona).

4. Zostanie otwarta strona **Reguła tworzenia,** jak pokazano poniżej. Wypełnij wymagane informacje:

   ![Dodaj formularz alertu metryki](./media/howto-alert-on-metric/4-add-rule-form.png)

5. W sekcji **Warunek** wybierz pozycję **Dodaj warunek**.

6. Wybierz metrykę z listy sygnałów, które mają być alertowane. W tym przykładzie wybierz "Procent magazynu".
   
   ![Wybierz metrykę](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Skonfiguruj logikę alertu, w tym **Warunek** (np. "Większa niż"), **Próg** (np. 85 procent), **Agregacja czasu,** **Okres** czasu reguła metryki musi zostać spełniona przed wyzwoleniem alertu (np. "W ciągu ostatnich 30 minut") i **Częstotliwość**.
   
   Po zakończeniu wybierz **pozycję Gotowe.**

   ![Wybierz metrykę](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. W sekcji **Grupy akcji** wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę, która będzie otrzymywać powiadomienia w alertie.

9. Wypełnij formularz "Dodaj grupę akcji" nazwą, krótką nazwą, subskrypcją i grupą zasobów.

10. Skonfiguruj typ akcji **e-mail/SMS/Push/Voice.**
    
    Wybierz opcję "Rola usługi Azure Resource Manager poczty e-mail", aby wybrać właścicieli subskrypcji, współautorów i czytników, aby otrzymywać powiadomienia.
   
    Opcjonalnie podaj prawidłowy identyfikator URI w polu **Element webhook,** jeśli chcesz, aby został wywołany podczas uruchamiania alertu.

    Po zakończeniu wybierz **przycisk OK.**

    ![Grupa akcji](./media/howto-alert-on-metric/10-action-group-type.png)

11. Określ nazwę reguły alertu, opis i ważność.

    ![Grupa akcji](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Wybierz **pozycję Utwórz regułę alertu,** aby utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwala, jak opisano wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu można go wybrać i wykonać następujące czynności:

* Wyświetl wykres przedstawiający próg metryki i rzeczywiste wartości z poprzedniego dnia istotne dla tego alertu.
* **Edytuj** lub **usuń** regułę alertu.
* **Wyłącz** lub **włącz** alert, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Zapoznaj się [z omówieniem kolekcji metryk,](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) aby upewnić się, że usługa jest dostępna i responsywna.
