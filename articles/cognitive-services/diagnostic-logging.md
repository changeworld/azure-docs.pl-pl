---
title: Rejestrowanie diagnostyczne
titleSuffix: Azure Cognitive Services
description: Ten przewodnik zawiera instrukcje krok po kroku, aby włączyć rejestrowanie diagnostyczne dla usługi Azure Cognitive Service. Dzienniki te zapewniają zaawansowane, często dane o zasobu, które są używane do identyfikacji problemu i debugowania.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155732"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Włączanie rejestrowania diagnostycznego dla usług Azure Cognitive Services

Ten przewodnik zawiera instrukcje krok po kroku, aby włączyć rejestrowanie diagnostyczne dla usługi Azure Cognitive Service. Dzienniki te zapewniają zaawansowane, często dane o zasobu, które są używane do identyfikacji problemu i debugowania. Przed kontynuowaniem, konieczne jest posiadanie konta platformy Azure z subskrypcją co najmniej jedna usługa Cognitive, takich jak [wyszukiwania w Internecie Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [usług przetwarzania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), lub [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie diagnostyczne, konieczne będzie miejsce do przechowywania danych dziennika. Ten samouczek używa usługi Azure Storage i usługi Log Analytics.

* [Usługa Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -zachowuje dzienniki diagnostyczne na potrzeby zasad inspekcji, analizę statyczną lub kopii zapasowej. Konto magazynu nie musi znajdować się w tej samej subskrypcji co zasób emitowane dzienniki, jak długo użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) — elastyczna dziennika analizy i wyszukiwania narzędzie, które umożliwia analizy nieprzetworzonych dzienników generowanych przez zasób platformy Azure.

> [!NOTE]
> Dodatkowe opcje konfiguracji są dostępne. Aby dowiedzieć się więcej, zobacz [zbieranie i używanie dane dzienników z zasobów platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-diagnostic-log-collection"></a>Włączanie zbierania dzienników diagnostycznych  

Zacznijmy od włączania diagnostyki rejestrowanie przy użyciu witryny Azure portal.

> [!NOTE]
> Aby włączyć tę funkcję za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, należy użyć w instrukcjach podanych w [zbieranie i używanie dane dzienników z zasobów platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

1. Przejdź do witryny Azure portal. Następnie znajdź i wybierz zasób usług Cognitive Services. Na przykład subskrypcji do wyszukiwania w Internecie Bing.   
2. Następnie z menu nawigacji po lewej stronie Znajdź **monitorowanie** i wybierz **ustawień diagnostycznych**. Ten ekran zawiera wszystkie wcześniej utworzone ustawienia diagnostyczne dla tego zasobu.
3. W przypadku wcześniej utworzonego zasobu, którego chcesz użyć, możesz wybrać je teraz. W przeciwnym razie wybierz **+ Dodaj ustawienie diagnostyczne**.
4. Wprowadź nazwę dla ustawienia. Następnie wybierz pozycję **Zarchiwizuj na koncie magazynu** i **wysyłać log Analytics**.
5. Po wyświetleniu monitu, aby skonfigurować, wybierz konto magazynu i obszar roboczy pakietu OMS, które chcesz użyć do przechowywania możesz dzienników diagnostycznych. **Uwaga**: Jeśli nie masz konta magazynu lub obszar roboczy pakietu OMS, postępuj zgodnie z monitami, aby go utworzyć.
6. Wybierz **inspekcji**, **Element RequestResponse**, i **AllMetrics**. Następnie ustaw okres przechowywania danych dzienników diagnostycznych. Jeśli zasady przechowywania jest ustawiony na wartość zero, zdarzenia dla tej kategorii dziennika są przechowywane przez czas nieokreślony.
7. Kliknij pozycję **Zapisz**.

Może upłynąć do dwóch godzin, zanim dane rejestrowania są dostępne dla zapytań i analizowania. Więc nie martw się, jeśli nie widzisz żadnych natychmiast.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Wyświetlanie i eksportowanie danych diagnostycznych z usługi Azure Storage

Usługa Azure Storage to rozwiązanie służące do magazynowania obiektu niezawodne, zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. W tej sekcji dowiesz się, zapytanie konta magazynu dla wszystkich transakcji, dotyczące przedziału czasu 30-dniowej i eksportowanie danych do programu excel.

1. W witrynie Azure portal zlokalizuj zasobu usługi Azure Storage, który został utworzony w ostatniej sekcji.
2. W menu nawigacji po lewej stronie Znajdź **monitorowanie** i wybierz **metryki**.
3. Aby skonfigurować zapytania, należy użyć dostępne listy rozwijane. W tym przykładzie załóżmy Ustaw zakres czasu **ostatnie 30 dni** i metrykę, aby **transakcji**.
4. Gdy zapytanie zostanie zakończone, zobaczysz wizualizację transakcji w ciągu ostatnich 30 dni. Aby wyeksportować te dane, należy użyć **Eksportuj do programu Excel** znajdujący się w górnej części strony.

Dowiedz się więcej o co można zrobić z danymi diagnostycznymi w [usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

Wykonaj te instrukcje, aby eksplorować dane analizy dziennika dla zasobu.

1. W witrynie Azure portal, Znajdź i zaznacz **usługi Log Analytics** menu nawigacji po lewej stronie.
2. Zlokalizuj i wybierz zasób, który został utworzony podczas włączania diagnostyki.
3. W obszarze **ogólne**Znajdź i zaznacz **dzienniki**. Na tej stronie można uruchomić zapytania dla dzienników.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kilka podstawowe zapytania Kusto, których można użyć, aby eksplorować dane dziennika.

Uruchom to zapytanie dla wszystkich dzienników diagnostycznych z usług Azure Cognitive Services w określonym przedziale czasu:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Uruchom to zapytanie, aby wyświetlić dzienniki ostatnich 10:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Uruchom to zapytanie do grupy o operacjach **zasobów**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Uruchom ten zapytań można znaleźć średni czas potrzebny do wykonania operacji:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Wykonania tego zapytania, aby wyświetlić ilość operacji wraz z upływem czasu podział według OperationName z dużymi liczbami kwanty dla każdego 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Kolejne kroki

* Aby zrozumieć, jak włączyć rejestrowanie i kategorie metryk i dzienników, które są obsługiwane przez różne usługi platformy Azure, przeczytaj zarówno [Przegląd metryk](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) w systemie Microsoft Azure i [Omówienie programu Azure dzienników diagnostycznych ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) artykułów.
* Przeczytaj następujące artykuły, aby dowiedzieć się więcej o usłudze event hubs:
  * [Co to jest usługa Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Rozpoczynanie pracy z usługą Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Odczyt [pobrać metryk i dzienników diagnostycznych z usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Odczyt [omówienie wyszukiwań w dziennikach w usłudze Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
