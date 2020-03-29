---
title: Rejestrowanie diagnostyczne
titleSuffix: Azure Cognitive Services
description: Ten przewodnik zawiera instrukcje krok po kroku, aby włączyć rejestrowanie diagnostyczne dla usługi Azure Cognitive Service. Te dzienniki zapewniają bogate, częste dane dotyczące działania zasobu, które są używane do identyfikacji problemów i debugowania.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827894"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Włączanie rejestrowania diagnostycznego dla usług Azure Cognitive Services

Ten przewodnik zawiera instrukcje krok po kroku, aby włączyć rejestrowanie diagnostyczne dla usługi Azure Cognitive Service. Te dzienniki zapewniają bogate, częste dane dotyczące działania zasobu, które są używane do identyfikacji problemów i debugowania. Przed kontynuowaniem należy mieć konto platformy Azure z subskrypcją co najmniej jednej usługi Cognitive Service, takiej jak [wyszukiwanie w sieci Web Bing,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)usługi [mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)lub [usługa LUIS.](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie diagnostyczne, musisz gdzieś przechowywać dane dziennika. W tym samouczku użyto usługi Azure Storage i usługi Log Analytics.

* [Magazyn platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) — zachowuje dzienniki diagnostyczne do inspekcji zasad, analizy statycznej lub kopii zapasowej. Konto magazynu nie musi znajdować się w tej samej subskrypcji co dzienniki emitujące zasób, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC do obu subskrypcji.
* [Usługa Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) — elastyczne narzędzie do wyszukiwania i analizy dzienników, które umożliwia analizę nieprzetworzonych dzienników generowanych przez zasób platformy Azure.

> [!NOTE]
> Dostępne są dodatkowe opcje konfiguracji. Aby dowiedzieć się więcej, zobacz [Zbieranie i korzystanie z danych dziennika z zasobów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Włączanie zbierania dzienników diagnostycznych  

Zacznijmy od włączenia rejestrowania diagnostycznego przy użyciu witryny Azure portal.

> [!NOTE]
> Aby włączyć tę funkcję przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, należy użyć instrukcji podanych w [kolekcji Collect i korzystać z danych dziennika z zasobów platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

1. Przejdź do witryny Azure Portal. Następnie znajdź i wybierz zasób usług Cognitive Services. Na przykład subskrypcja usługi Bing Web Search.   
2. Następnie z menu nawigacji po lewej stronie znajdź **pozycję Monitorowanie** i wybierz pozycję **Ustawienia diagnostyczne**. Ten ekran zawiera wszystkie wcześniej utworzone ustawienia diagnostyczne dla tego zasobu.
3. Jeśli istnieje wcześniej utworzony zasób, którego chcesz użyć, możesz go teraz wybrać. W przeciwnym razie wybierz **opcję + Dodaj ustawienie diagnostyczne**.
4. Wprowadź nazwę tego ustawienia. Następnie wybierz **opcję Archiwizuj na konto magazynu** i Wyślij do dziennika **Analytics**.
5. Po wyświetleniu monitu o skonfigurowanie wybierz konto magazynu i obszar roboczy usługi OMS, którego chcesz użyć do przechowywania dzienników diagnostycznych. **Uwaga:** Jeśli nie masz konta magazynu lub obszaru roboczego oms, postępuj zgodnie z instrukcjami, aby je utworzyć.
6. Wybierz **pozycję Inspekcja,** **RequestResponse**i **AllMetrics**. Następnie ustaw okres przechowywania danych dziennika diagnostycznego. Jeśli zasady przechowywania jest ustawiona na zero, zdarzenia dla tej kategorii dziennika są przechowywane przez czas nieokreślony.
7. Kliknij przycisk **Zapisz**.

Może upłynąć do dwóch godzin, zanim dane logowania są dostępne do kwerendy i analizy. Więc nie martw się, jeśli nie widzisz nic od razu.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Wyświetlanie i eksportowanie danych diagnostycznych z usługi Azure Storage

Usługa Azure Storage to niezawodne rozwiązanie magazynu obiektów, które jest zoptymalizowane pod kątem przechowywania dużych ilości danych nieustrukturyzowanych. W tej sekcji dowiesz się, aby zbadać konto magazynu dla łącznej liczby transakcji w ciągu 30-dniowego przedziału czasowego i wyeksportować dane do programu Excel.

1. W witrynie Azure portal znajdź zasób usługi Azure Storage utworzony w ostatniej sekcji.
2. Z menu nawigacji po lewej stronie znajdź **pozycję Monitorowanie** i wybierz **pozycję Metryki**.
3. Użyj dostępnych rozwijanych, aby skonfigurować zapytanie. W tym przykładzie ustawmy zakres czasu na **Ostatnie 30 dni,** a metrykę na **Transakcję**.
4. Po zakończeniu kwerendy zostanie wyświetlenia wizualizacji transakcji w ciągu ostatnich 30 dni. Aby wyeksportować te dane, użyj przycisku **Eksportuj do programu Excel** znajdującego się u góry strony.

Dowiedz się więcej o tym, co można zrobić z danymi diagnostycznymi w [usłudze Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

Postępuj zgodnie z tymi instrukcjami, aby eksplorować dane analizy dzienników dla zasobu.

1. W witrynie Azure portal znajdź i wybierz **usługę Log Analytics** z menu nawigacji po lewej stronie.
2. Znajdź i wybierz zasób utworzony podczas włączania diagnostyki.
3. W **obszarze Ogólne**znajdź i wybierz pozycję **Dzienniki**. Na tej stronie można uruchamiać zapytania względem dzienników.

### <a name="sample-queries"></a>Przykładowe zapytania

Oto kilka podstawowych zapytań Kusto, których możesz użyć do eksplorowania danych dziennika.

Uruchom tę kwerendę dla wszystkich dzienników diagnostycznych z usług Azure Cognitive Services przez określony czas:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Uruchom tę kwerendę, aby wyświetlić 10 najnowszych dzienników:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Uruchom tę kwerendę w celu grupowania operacji według **zasobu:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Uruchom tę kwerendę, aby znaleźć średni czas potrzebny do wykonania operacji:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Uruchom tę kwerendę, aby wyświetlić ilość operacji w czasie podzielonych przez OperationName z liczbami binned dla każdego 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak włączyć rejestrowanie, a także metryki i kategorie dzienników, które są obsługiwane przez różne usługi platformy Azure, przeczytaj zarówno [omówienie metryk](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) na platformie Microsoft Azure, jak i [omówienie artykułów dziennika diagnostycznego platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)
* Przeczytaj te artykuły, aby dowiedzieć się więcej o centrach zdarzeń:
  * [Co to jest usługa Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Rozpoczynanie pracy z usługą Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Przeczytaj [dane pobierania i dzienniki diagnostyczne z usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Przeczytaj [opis wyszukiwania dzienników w dziennikach usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
