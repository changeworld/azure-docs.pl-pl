---
title: Monitorowanie funkcji platformy Azure za pomocą dzienników monitora platformy Azure
description: Dowiedz się, jak używać dzienników usługi Azure Monitor z usługami Azure Functions do monitorowania wykonywania funkcji.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649878"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorowanie funkcji platformy Azure za pomocą dzienników monitora platformy Azure

Usługa Azure Functions oferuje integrację z [dziennikami usługi Azure Monitor](../azure-monitor/platform/data-platform-logs.md) w celu monitorowania funkcji. W tym artykule pokazano, jak skonfigurować usługi Azure Functions do wysyłania dzienników generowanych przez system i generowanych przez użytkownika do dzienników usługi Azure Monitor.

Dzienniki usługi Azure Monitor umożliwiają konsolidację dzienników z różnych zasobów w tym samym obszarze roboczym, gdzie można je analizować za pomocą [zapytań](../azure-monitor/log-query/log-query-overview.md) w celu szybkiego pobierania, konsolidowania i analizowania zebranych danych.  Można tworzyć i testować kwerendy przy użyciu [usługi Log Analytics](../azure-monitor/log-query/portals.md) w witrynie Azure portal, a następnie bezpośrednio analizować dane za pomocą tych narzędzi lub zapisywać zapytania do użycia z wizualizacjami lub [regułami alertów.](../azure-monitor/visualizations.md) [alert rules](../azure-monitor/platform/alerts-overview.md)

Usługa Azure Monitor używa wersji [języka zapytań Kusto](/azure/kusto/query/) używanego przez Eksploratora danych platformy Azure, który jest odpowiedni dla prostych zapytań dziennika, ale zawiera również zaawansowane funkcje, takie jak agregacje, sprzężenia i inteligentne analizy. Możesz szybko nauczyć się języka zapytań, korzystając z [wielu lekcji.](../azure-monitor/log-query/get-started-queries.md)

> [!NOTE]
> Integracja z dziennikami monitora platformy Azure jest obecnie w publicznej wersji zapoznawczej dla aplikacji funkcji działających w systemach Windows Consumption, Premium i dedykowanych planach hostingowych.

## <a name="setting-up"></a>Konfigurowanie

W sekcji **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne,** a następnie kliknij pozycję **Dodaj ustawienie diagnostyczne**.

![Dodawanie ustawienia diagnostycznego](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na stronie **Ustawienia diagnostyki** wybierz pozycję **Wyślij do usługi Log Analytics**, a następnie wybierz obszar roboczy usługi Log Analytics. W **obszarze dziennik** wybierz **functionapplogs**, ta tabela zawiera żądane dzienniki.

![Dodawanie ustawienia diagnostycznego](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Dzienniki generowane przez użytkowników

Aby wygenerować dzienniki niestandardowe, można użyć określonej instrukcji rejestrowania w zależności od języka, oto przykładowe fragmenty kodu:


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Wykonywanie zapytań o dzienniki

Aby zbadać wygenerowane dzienniki, przejdź do obszaru roboczego usługi Log Analytics skonfigurowany do wysyłania dzienników funkcji i kliknij pozycję **Dzienniki**.

![Okno kwerendy w obszarze roboczym LA](media/functions-monitor-log-analytics/querying.png)

Usługa Azure Functions zapisuje wszystkie dzienniki w tabeli **FunctionAppLogs,** oto kilka przykładowych zapytań.

### <a name="all-logs"></a>Wszystkie dzienniki

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Określona funkcja rejestruje

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Wyjątki

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem funkcji platformy Azure](functions-overview.md)
- Dowiedz się więcej o [dziennikach monitora platformy Azure](../azure-monitor/platform/data-platform-logs.md)
- Dowiedz się więcej o [języku zapytań](../azure-monitor/log-query/get-started-queries.md).
