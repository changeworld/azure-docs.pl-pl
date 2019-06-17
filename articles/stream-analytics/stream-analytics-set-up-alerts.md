---
title: Konfigurowanie monitorowania alertów dotyczących zadań usługi Azure Stream Analytics
description: W tym artykule opisano jak skonfigurować monitorowanie i alerty dla zadań usługi Azure Stream Analytics za pomocą witryny Azure portal.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 09a5c77c770f7709b90d4959da5be9134b0f0ad9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077679"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurowanie alertów dotyczących zadań usługi Azure Stream Analytics

Należy monitorować zadania usługi Azure Stream Analytics do upewnij się, że zadanie działa nieprzerwanie, bez problemów. W tym artykule opisano sposób konfigurowania alertów dla typowych scenariuszy, które powinny być monitorowane. 

Można zdefiniować reguły dotyczące metryk z danych dzienników operacji za pośrednictwem portalu, a także [programowo](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurowanie alertów w witrynie Azure portal

Poniższy przykład pokazuje, jak skonfigurować alerty, gdy zadanie przechodzi do stanu nie powiodło się. Ten alert jest zalecana dla wszystkich zadań.

1. W witrynie Azure portal Otwórz zadanie usługi Stream Analytics ma zostać utworzony alert w przypadku.

2. Na **zadania** stronie, przejść do **monitorowanie** sekcji.  

3. Wybierz **metryki**, a następnie **Nowa reguła alertu**.

   ![Ustawienia alertów usługi Stream Analytics portalu Azure](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Nazwa zadania usługi Stream Analytics powinny automatycznie wyświetlane w obszarze **zasobów**. Kliknij przycisk **Dodaj warunek**i wybierz **operacje administracyjne wszystkich** w obszarze **konfigurowanie logiki sygnału**.

   ![Wybierz nazwę sygnał alertu usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. W obszarze **konfigurowanie logiki sygnału**, zmień **poziom zdarzenia** do **wszystkich** i zmień **stan** do **nie powiodło się** . Pozostaw **zdarzenie zainicjowane przez** puste i wybierz **gotowe**.

   ![Konfigurowanie logiki sygnału dla alertu usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Wybierz istniejącą grupę akcji lub Utwórz nową grupę. W tym przykładzie Nowa grupa akcji o nazwie **TIDashboardGroupActions** został utworzony za pomocą **wiadomości E-mail** akcję, która wysyła wiadomość e-mail do użytkowników za pomocą **właściciela** zasobów platformy Azure Rola menedżera.

   ![Konfigurowanie alertu dla zadania usługi Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Zasobów**, **WARUNEK**, i **grup akcji** ma wpis. Należy pamiętać, że aby wyzwalać alerty, zdefiniowanych warunków muszą zostać spełnione. Można na przykład mierzyć co 5 minut średnią wartość metryki z ostatnich 15 minut.

   ![Utwórz regułę alertu usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Dodaj **Nazwa reguły alertu**, **opis**i **grupy zasobów** do **szczegóły ALERTU** i kliknij przycisk **Utwórz alert Reguła** do utworzenia reguły dla zadania usługi Stream Analytics.

   ![Utwórz regułę alertu usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scenariusze monitorowania

Następujące alerty są zalecane do monitorowania wydajności zadania usługi Stream Analytics. Te metryki powinna być oceniana na minutę w ostatnim okresie 5 minut.

|Metryka|Warunek|Agregacja czasu|Próg|Akcje naprawcze|
|-|-|-|-|-|
|% Wykorzystania SU|Większe niż|Maksimum|80|Istnieje wiele czynników, które zwiększają SU % wykorzystania. Można skalowanie za pomocą przetwarzania równoległego zapytań lub zwiększyć liczbę jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z przetwarzania równoległego zapytań w usłudze Azure Stream Analytics](stream-analytics-parallelization.md).|
|Błędy czasu wykonywania|Większe niż|Łącznie|0|Sprawdź działanie lub dzienniki diagnostyczne i wprowadzenie odpowiednich zmian w danych wejściowych, zapytania ani danych wyjściowych.|
|Opóźnienie znaku wodnego|Większe niż|Maksimum|Gdy średnia wartość ta metryka w ciągu ostatnich 15 minut jest większa niż tolerancja spóźnionego przybycia (w sekundach). W przypadku braku modyfikacji tolerancja spóźnionego przybycia wartość domyślna jest równa 5 sekund.|Spróbuj zwiększyć liczbę SUs lub zrównoleglić zapytanie. Aby uzyskać więcej informacji na temat usług SUs, zobacz [opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Aby uzyskać więcej informacji na temat zrównoleglić zapytanie, zobacz [wykorzystywanie przetwarzania równoległego zapytań w usłudze Azure Stream Analytics](stream-analytics-parallelization.md).|
|Błędy deserializacji danych wejściowych|Większe niż|Łącznie|0|Sprawdź działanie lub dzienniki diagnostyczne i wprowadzić odpowiednie zmiany w danych wejściowych. Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać więcej informacji na temat konfigurowania alertów w witrynie Azure portal, zobacz [otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-get-started.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

