---
title: Konfigurowanie alertów i powiadomień (Azure portal)
description: Użyj witryny Azure Portal do tworzenia alertów bazy danych SQL, które mogą wyzwalać powiadomienia lub automatyzację po spełnieniu określonych warunków.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209485"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Tworzenie alertów dla baz danych usługi Azure SQL Database i usługi Azure Synapse Analytics przy użyciu witryny Azure portal

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak skonfigurować alerty dla pojedynczych, pulowanych i magazynów danych baz danych w usłudze Azure SQL Database i usłudze Azure Synapse Analytics (dawniej Usługa Azure SQL Data Warehouse) przy użyciu witryny Azure portal. Alerty mogą wysyłać wiadomości e-mail lub wywoływać hak internetowy, gdy niektóre metryki (na przykład rozmiar bazy danych lub użycie procesora CPU) osiągnie próg. Ten artykuł zawiera również najlepsze rozwiązania dotyczące ustawiania okresów alertów.

> [!IMPORTANT]
> Ta funkcja nie jest jeszcze dostępna w wystąpieniu zarządzanym. Alternatywnie można użyć programu SQL Agent do wysyłania alertów e-mail dla niektórych metryk opartych na [widokach dynamicznego zarządzania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Możesz otrzymać alert na podstawie monitorowania metryk lub zdarzeń na usługi platformy Azure.

* **Wartości metryki** — alert wyzwala, gdy wartość określonej metryki przekracza próg przypisany w obu kierunkach. Oznacza to, że wyzwala zarówno wtedy, gdy warunek jest po raz pierwszy spełnione, a następnie później, gdy warunek ten nie jest już spełniony.
* **Zdarzenia dziennika aktywności** — alert może wyzwolić się w *każdym* zdarzeniu lub tylko wtedy, gdy wystąpi określona liczba zdarzeń.

Alert można skonfigurować tak, aby podczas wyzwalania wyzwalał następujące czynności:

* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail na dodatkowe określone wiadomości e-mail.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów za pomocą

* [Portal Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Powershell](../azure-monitor/platform/alerts-classic-portal.md)
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
  ![Wybierz](media/sql-database-insights-alerts-portal/select-signal.png)sygnał .
6. Po wybraniu sygnału, takiego jak **procent procesora,** zostanie wyświetlona strona **Konfiguruj logikę sygnału.**
  ![Konfigurowanie logiki sygnału](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Na tej stronie skonfiguruj ten typ progu, operator, typ agregacji, wartość progową, szczegółowość agregacji i częstotliwość oceny. Następnie kliknij przycisk **Gotowe**.
8. W **regule Utwórz**wybierz istniejącą **grupę Akcji** lub utwórz nową grupę. Grupa akcji umożliwia zdefiniowanie akcji, które mają zostać podjęte w przypadku wystąpienia warunku alertu.
  ![Definiowanie grupy akcji](media/sql-database-insights-alerts-portal/action-group.png)

9. Zdefiniuj nazwę reguły, podaj opcjonalny opis, wybierz poziom ważności dla reguły, wybierz, czy regułę włączyć podczas tworzenia reguły, a następnie kliknij przycisk **Utwórz alert reguły,** aby utworzyć alert reguły metryki.

W ciągu 10 minut alert jest aktywny i wyzwala zgodnie z wcześniejszym opisem.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
