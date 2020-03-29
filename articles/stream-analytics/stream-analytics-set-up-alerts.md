---
title: Konfigurowanie alertów monitorowania dla zadań usługi Azure Stream Analytics
description: W tym artykule opisano sposób używania witryny Azure Portal do konfigurowania monitorowania i alertów dla zadań usługi Azure Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458720"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurowanie alertów dla zadań usługi Azure Stream Analytics

Ważne jest, aby monitorować zadanie usługi Azure Stream Analytics, aby upewnić się, że zadanie jest uruchomione w sposób ciągły bez żadnych problemów. W tym artykule opisano sposób konfigurowania alertów dla typowych scenariuszy, które powinny być monitorowane. 

Reguły dotyczące metryk z danych dzienników operacji można definiować za pośrednictwem portalu, a także [programowo.](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurowanie alertów w witrynie Azure portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Otrzymuj alerty, gdy zadanie zostanie nieoczekiwanie zatrzymane

W poniższym przykładzie pokazano, jak skonfigurować alerty, gdy zadanie wchodzi w stan awarii. Ten alert jest zalecany dla wszystkich zadań.

1. W witrynie Azure portal otwórz zadanie usługi Stream Analytics, dla którego chcesz utworzyć alert.

2. Na stronie **Zadanie** przejdź do sekcji **Monitorowanie.**  

3. Wybierz **pozycję Metryki**, a następnie **pozycję Nowa reguła alertu**.

   ![Konfiguracja alertów usługi Azure portal Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Nazwa zadania usługi Stream Analytics powinna być automatycznie wyświetlana w obszarze **ZASÓB**. Kliknij pozycję **Dodaj warunek**i wybierz pozycję Wszystkie **operacje administracyjne** w obszarze **Konfigurowanie logiki sygnału**.

   ![Wybierz nazwę sygnału dla alertu usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. W obszarze **Konfigurowanie logiki sygnału**zmień **poziom zdarzenia** na **Wszystkie** i zmień **stan** na **Niewiedzy**. Pozostaw **zdarzenie zainicjowane przez** puste i wybierz pozycję **Gotowe**.

   ![Konfigurowanie logiki sygnału dla alertu usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Wybierz istniejącą grupę akcji lub utwórz nową grupę. W tym przykładzie utworzono nową grupę akcji o nazwie **TIDashboardGroupActions** z akcją **Wiadomości e-mail,** która wysyła wiadomość e-mail do użytkowników z rolą menedżera zasobów **azure właściciela.**

   ![Konfigurowanie alertu dla zadania usługi Azure Streaming Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Zasób,** **WARUNEK**i **grupy akcji** powinny mieć wpis. Należy pamiętać, że aby alerty do ognia, warunki określone muszą być spełnione. Można na przykład mierzyć co 5 minut średnią wartość metryki z ostatnich 15 minut.

   ![Reguła alertów tworzenie usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Dodaj **nazwę reguły alertu**, **Opis**i **grupę zasobów** do **SZCZEGÓŁÓW ALERTU** i kliknij pozycję **Utwórz regułę alertu,** aby utworzyć regułę dla zadania usługi Stream Analytics.

   ![Reguła alertów tworzenie usługi Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scenariusze do monitorowania

Następujące alerty są zalecane do monitorowania wydajności zadania usługi Stream Analytics. Te metryki powinny być oceniane co minutę w ciągu ostatniego okresu 5 minut.

|Metryka|Warunek|Agregacja czasu|Próg|Działania naprawcze|
|-|-|-|-|-|
|Wykorzystanie SU%|Większe niż|Maksimum|80|Istnieje wiele czynników, które zwiększają wykorzystanie SU%. Można skalować za pomocą równoległości kwerendy lub zwiększyć liczbę jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z przetwarzania równoległego zapytań w usłudze Azure Stream Analytics](stream-analytics-parallelization.md).|
|Błędy środowiska uruchomieniowego|Większe niż|Łącznie|0|Sprawdź dzienniki działania lub diagnostyki i wprowadź odpowiednie zmiany w danych wejściowych, kwerendzie lub wyjściach.|
|Opóźnienie znaku wodnego|Większe niż|Maksimum|Gdy średnia wartość tej metryki w ciągu ostatnich 15 minut jest większa niż tolerancja późnego przybycia (w sekundach). Jeśli tolerancja późnego przybycia nie została zmodyfikowana, wartość domyślna jest ustawiona na 5 sekund.|Spróbuj zwiększyć liczbę sus lub równoległości kwerendy. Aby uzyskać więcej informacji na temat jednostek SU, zobacz [Rozumienie i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Aby uzyskać więcej informacji na temat równoległości kwerendy, zobacz [Inicjowanie równoległości zapytań w usłudze Azure Stream Analytics.](stream-analytics-parallelization.md)|
|Błędy deserializacji danych wejściowych|Większe niż|Łącznie|0|Sprawdź dzienniki aktywności lub diagnostyki i wprowadź odpowiednie zmiany w danych wejściowych. Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać więcej informacji na temat konfigurowania alertów w witrynie Azure portal, zobacz [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-get-started.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

