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
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038888"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych

Funkcja diagnostyki chmury wiosennej platformy Azure umożliwia analizowanie dzienników i metryk przy użyciu jednej z następujących usług:

* Analizuj je za pomocą usługi Azure Log Analytics, w której dane są zapisywane natychmiast na platformie Azure Log Analytics bez konieczności wcześniejszego zapisywania danych w magazynie.
* Zapisz je na koncie magazynu, aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach).
* Przesyłaj strumieniowo do Event Hubs na potrzeby pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej.

Aby rozpocząć, musisz włączyć jedną z tych usług, aby otrzymywać dane.  Aby dowiedzieć się więcej o konfigurowaniu Log Analytics, zapoznaj się z [tym samouczkiem](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

1. Przejdź do wystąpienia chmury Azure wiosny w Azure Portal.
1. Wybierz opcję menu **Ustawienia diagnostyczne** .
1. Wybierz przycisk **Dodaj ustawienie diagnostyczne** .
1. Wprowadź nazwę ustawienia i wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację następujących trzech opcji:
    * Archiwizowanie na koncie magazynu
    * Przesyłanie strumieniowe do centrum zdarzeń
    * Wysyłanie do usługi Log Analytics

1. Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować, a następnie określ czas przechowywania (w dniach). Czas przechowywania ma zastosowanie tylko do konta magazynu.
1. Wybierz pozycję **Zapisz** , aby zastosować ustawienie.

> [!NOTE]
> Gdy dzienniki lub metryki są emitowane i wyświetlane w ramach konta magazynu/centrum zdarzeń/Log Analytics, może wystąpić do 15 minut.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

### <a name="using-log-analytics"></a>Korzystanie z usługi Log Analytics

1. Z Azure Portal wybierz pozycję Log Analytics z menu nawigacji po lewej stronie.
1. Wybierz obszar roboczy Log Analytics wybrany podczas dodawania ustawień diagnostycznych.
1. Wybierz `Logs`, aby otworzyć blok przeszukiwanie dzienników.
1. Wprowadź proste zapytanie do pola wyszukiwania w dzienniku.  Na przykład:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Wybierz pozycję `Run`, aby wyświetlić wynik wyszukiwania.
1. Dzienniki konkretnej aplikacji lub wystąpienia można wyszukać, ustawiając warunek filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Dowiedz się więcej o języku zapytań używanym w Log Analytics [w tym artykule](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>Korzystanie z dzienników i metryk na koncie magazynu

1. W Azure Portal wybierz pozycję konta magazynu z menu nawigacji po lewej stronie.
1. Wybierz konto magazynu wybrane podczas dodawania ustawień diagnostycznych.
1. Wybierz pozycję `Blobs`, aby otworzyć blok kontenera obiektów BLOB.
1. Znajdź kontener o nazwie `insights-logs-applicationconsole` w celu przejrzenia dzienników aplikacji.
1. Znajdź kontener o nazwie `insights-metrics-pt1m`, aby przejrzeć metryki aplikacji.

[Dowiedz się więcej o wysyłaniu informacji diagnostycznych do konta magazynu.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Używanie Event Hubs

1. Z Azure Portal wybierz pozycję Event Hubs z menu nawigacji po lewej stronie.
1. Znajdź i wybierz Event Hubs wybrany podczas dodawania ustawień diagnostycznych.
1. Wybierz `Event Hubs`, aby otworzyć blok lista centrum zdarzeń.
1. Znajdź centrum zdarzeń o nazwie `insights-logs-applicationconsole` w celu przejrzenia dzienników aplikacji.
1. Znajdź centrum zdarzeń o nazwie `insights-metrics-pt1m`, aby przejrzeć metryki aplikacji.

[Dowiedz się więcej o wysyłaniu informacji diagnostycznych do centrum zdarzeń.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>Analizowanie dzienników

Usługa Azure Log Analytics udostępnia Kusto, dzięki czemu możesz wysyłać zapytania do dzienników w celu analizy.  Zapoznaj się z [samouczkiem log Analytics](../azure-monitor/log-query/get-started-portal.md) , aby uzyskać krótkie wprowadzenie do wykonywania zapytań dotyczących dzienników przy użyciu Kusto.

Dzienniki aplikacji zawierają krytyczne informacje o kondycji, wydajności i innych aplikacjach.  Poniżej znajdują się proste zapytania, które ułatwiają zrozumienie aktualnych i przeszłych stanów aplikacji.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Wyświetlanie dzienników aplikacji z chmury Azure wiosennej

Aby zapoznać się z listą dzienników aplikacji z chmury Azure wiosennej, posortowanej według czasu z najnowszymi wyświetlanymi dziennikami:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Pokaż wpisy dzienników zawierające błędy lub wyjątki

To zapytanie umożliwia przejrzenie wpisów dziennika, które opisują błąd lub wyjątek.  Wyniki nie są sortowane.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Użyj tego zapytania, aby znaleźć błędy lub zmodyfikować terminy zapytania, aby znaleźć określone kody błędów lub wyjątki.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Pokaż liczbę błędów i wyjątków zgłoszonych przez aplikację w ciągu ostatniej godziny

To zapytanie tworzy wykres kołowy przedstawiający liczbę błędów i wyjątków zarejestrowanych przez aplikację w ciągu ostatniej godziny:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Dowiedz się więcej o wysyłaniu zapytań do dzienników aplikacji

Azure Monitor zapewnia szeroką pomoc techniczną dotyczącą wykonywania zapytań dotyczących dzienników aplikacji przy użyciu Log Analytics.  Aby dowiedzieć się więcej na temat tej usługi, zapoznaj się z samouczkiem dotyczącym [zapytań dzienników](../azure-monitor/log-query/get-started-queries.md) przy użyciu Azure monitor. [Przegląd zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md) zawiera więcej informacji na temat tworzenia zapytań w celu analizowania dzienników aplikacji.
