---
title: Analizowanie dzienników i metryk w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak analizować dane diagnostyczne w chmurze Azure wiosennej
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 955641f3511989baa5bfc3c0fa4d7df7ccbf9bfa
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554582"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych

Korzystając z funkcji diagnostyki chmury wiosennej platformy Azure, można analizować dzienniki i metryki przy użyciu dowolnej z następujących usług:

* Użyj usługi Azure Log Analytics, w której dane są zapisywane natychmiast, bez konieczności wcześniejszego zapisywania w magazynie.
* Zapisz je na koncie magazynu, aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach).
* Przesyłaj strumieniowo do centrum zdarzeń w celu pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej.

Aby rozpocząć, włącz jedną z tych usług, aby otrzymywać dane. Aby dowiedzieć się więcej o konfigurowaniu Log Analytics, zobacz [wprowadzenie do log Analytics w Azure monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Konfigurowanie ustawień diagnostycznych

1. W Azure Portal przejdź do wystąpienia chmury wiosennej platformy Azure.
1. Wybierz opcję **Ustawienia diagnostyczne** , a następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę ustawienia, a następnie wybierz miejsce, w którym chcesz wysłać dzienniki. Można wybrać dowolną kombinację następujących trzech opcji:
    * **Archiwizowanie na koncie magazynu**
    * **Przesyłanie strumieniowe do centrum zdarzeń**
    * **Wyślij do Log Analytics**

1. Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować, a następnie określ czas przechowywania (w dniach). Czas przechowywania ma zastosowanie tylko do konta magazynu.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Od momentu, gdy dzienniki lub metryki są emitowane i pojawiają się na koncie magazynu, centrum zdarzeń lub Log Analytics.

## <a name="view-the-logs"></a>Wyświetlanie dzienników

### <a name="use-log-analytics"></a>Korzystanie z usługi Log Analytics

1. W Azure Portal w lewym okienku wybierz pozycję **log Analytics**.
1. Wybierz obszar roboczy Log Analytics, który został wybrany podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **przeszukiwania dzienników** , wybierz pozycję **dzienniki**.
1. W polu wyszukiwania w **dzienniku** wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Aby wyświetlić wyniki wyszukiwania, wybierz pozycję **Uruchom**.
1. Dzienniki konkretnej aplikacji lub wystąpienia można wyszukać, ustawiając warunek filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Aby dowiedzieć się więcej o języku zapytań używanym w Log Analytics, zobacz [Azure monitor dziennika zapytań](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Korzystanie z konta magazynu 

1. W Azure Portal w okienku po lewej stronie wybierz pozycję **konta magazynu**.

1. Wybierz konto magazynu wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **kontenera obiektów BLOB** , wybierz pozycję **obiekty blob**.
1. Aby przejrzeć dzienniki aplikacji, Wyszukaj kontener o nazwie **Insights-Logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, Wyszukaj kontener o nazwie **Insights-Metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do konta magazynu, zobacz temat [Zapisywanie i wyświetlanie danych diagnostycznych w usłudze Azure Storage](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Korzystanie z centrum zdarzeń

1. W Azure Portal w lewym okienku wybierz pozycję **Event Hubs**.

1. Wyszukaj i wybierz centrum zdarzeń wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **Lista centrów zdarzeń** , wybierz pozycję **Event Hubs**.
1. Aby przejrzeć dzienniki aplikacji, Wyszukaj centrum zdarzeń o nazwie **Insights-Logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, Wyszukaj centrum zdarzeń o nazwie **Insights-Metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do centrum zdarzeń, zobacz [przesyłanie strumieniowe danych Diagnostyka Azure w ścieżce gorąca przy użyciu Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analizowanie dzienników

Usługa Azure Log Analytics udostępnia Kusto, dzięki czemu możesz wysyłać zapytania do dzienników w celu analizy. Aby zapoznać się z krótkim wprowadzeniem do wykonywania zapytań dotyczących dzienników przy użyciu Kusto, zapoznaj się z [samouczkiem log Analytics](../azure-monitor/log-query/get-started-portal.md).

Dzienniki aplikacji zawierają krytyczne informacje o kondycji, wydajności i innych aplikacjach. W następnych sekcjach znajdują się pewne proste zapytania, które pomagają zrozumieć bieżące i wcześniejsze Stany aplikacji.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Wyświetlanie dzienników aplikacji z chmury Azure wiosennej

Aby zapoznać się z listą dzienników aplikacji z chmury Azure wiosennej, posortowanej według czasu z najnowszymi wyświetlanymi dziennikami, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Pokaż wpisy dzienników zawierające błędy lub wyjątki

Aby przejrzeć niesortowane wpisy dziennika wskazujące błąd lub wyjątek, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Użyj tego zapytania, aby znaleźć błędy lub zmodyfikować terminy zapytania, aby znaleźć określone kody błędów lub wyjątki. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Pokaż liczbę błędów i wyjątków zgłoszonych przez aplikację w ciągu ostatniej godziny

Aby utworzyć wykres kołowy, który wyświetla liczbę błędów i wyjątków zarejestrowanych przez aplikację w ciągu ostatniej godziny, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Dowiedz się więcej o wysyłaniu zapytań do dzienników aplikacji

Azure Monitor zapewnia szeroką pomoc techniczną dotyczącą wykonywania zapytań dotyczących dzienników aplikacji przy użyciu Log Analytics. Aby dowiedzieć się więcej na temat tej usługi, zobacz [Rozpoczynanie pracy z kwerendami dzienników w Azure monitor](../azure-monitor/log-query/get-started-queries.md). Aby uzyskać więcej informacji na temat tworzenia zapytań w celu analizowania dzienników aplikacji, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).
