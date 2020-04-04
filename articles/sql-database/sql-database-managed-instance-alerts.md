---
title: Konfigurowanie alertów i powiadomień dotyczących wystąpienia zarządzanego (witryna Azure portal)
description: Użyj witryny Azure Portal do tworzenia alertów wystąpienia zarządzanego SQL, które mogą wyzwalać powiadomienia lub automatyzację po spełnieniu określonych warunków.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639200"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Tworzenie alertów dla wystąpienia zarządzanego sql platformy Azure przy użyciu portalu Azure

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak skonfigurować alerty dla baz danych w bazie danych zarządzanych wystąpień sql usługi Azure przy użyciu witryny Azure portal. Alerty mogą wysyłać wiadomość e-mail lub wywoływać hak internetowy, gdy niektóre metryki (na przykład rozmiar magazynu wystąpienia lub użycie procesora CPU) osiągnie próg. Ten artykuł zawiera również najlepsze rozwiązania dotyczące ustawiania okresów alertów.

Możesz otrzymać alert na podstawie monitorowania metryk lub zdarzeń na usługi platformy Azure.

* **Wartości metryki** — alert wyzwala, gdy wartość określonej metryki przekracza próg przypisany w obu kierunkach. Oznacza to, że wyzwala zarówno wtedy, gdy warunek jest po raz pierwszy spełnione, a następnie później, gdy warunek ten nie jest już spełniony.
* **Zdarzenia dziennika aktywności** — alert może wyzwolić się w *każdym* zdarzeniu lub tylko wtedy, gdy wystąpi określona liczba zdarzeń.

Alert można skonfigurować tak, aby podczas wyzwalania wyzwalał następujące czynności:

* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail na dodatkowe określone wiadomości e-mail.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów za pomocą

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [interfejs wiersza polecenia (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertów na metryki za pomocą witryny Azure portal

1. W [portalu](https://portal.azure.com/)znajdź zasób, który cię interesuje, i wybierz go.
2. Wybierz **pozycję Alerty** w sekcji Monitorowanie. Tekst i ikona mogą się nieznacznie różnić w zależności od zasobów.  

   ![Monitorowanie](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Wybierz przycisk **Nowa reguła alertu,** aby otworzyć stronę **Utwórz regułę.**
   ![Tworzenie reguły](media/sql-database-insights-alerts-portal/create-rule.png)

4. W sekcji **Warunek** kliknij pozycję **Dodaj**.
   ![Zdefiniuj warunek](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na stronie **Konfigurowanie logiki sygnału** wybierz sygnał.
   ![Wybierz sygnał](media/sql-database-insights-alerts-portal/select-signal.png)
6. Po wybraniu sygnału, takiego jak **procent procesora,** zostanie wyświetlona strona **Konfiguruj logikę sygnału.**
   ![Konfigurowanie logiki sygnału](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Na tej stronie skonfiguruj ten typ progu, operator, typ agregacji, wartość progową, szczegółowość agregacji i częstotliwość oceny. Następnie kliknij przycisk **Gotowe**.
8. W **regule Utwórz**wybierz istniejącą **grupę Akcji** lub utwórz nową grupę. Grupa akcji umożliwia zdefiniowanie akcji, które mają zostać podjęte w przypadku wystąpienia warunku alertu.
  ![Definiowanie grupy akcji](media/sql-database-insights-alerts-portal/action-group.png)

9. Zdefiniuj nazwę reguły, podaj opcjonalny opis, wybierz poziom ważności dla reguły, wybierz, czy regułę włączyć podczas tworzenia reguły, a następnie kliknij przycisk **Utwórz alert reguły,** aby utworzyć alert reguły metryki.

W ciągu 10 minut alert jest aktywny i wyzwala zgodnie z wcześniejszym opisem.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
