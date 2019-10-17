---
title: 'Informacje o interfejsie API usługi Azure Application Insights Agent: Konfiguracja | Microsoft Docs'
description: Dokumentacja interfejsu API agenta Application Insights. Set-ApplicationInsightsMonitoringConfig. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2ab941b5587a8836f1e472fbce3966b12bfa1e11
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388254"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Interfejs API agenta Application Insights: Set-ApplicationInsightsMonitoringConfig

W tym dokumencie opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Ustawia plik konfiguracji bez wykonywania pełnej ponownej instalacji.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.


## <a name="examples"></a>Przykłady

### <a name="example-with-a-single-instrumentation-key"></a>Przykład z pojedynczym kluczem Instrumentacji
W tym przykładzie do wszystkich aplikacji na bieżącym komputerze zostanie przypisany pojedynczy klucz Instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą kluczy Instrumentacji
W tym przykładzie:
- `MachineFilter` dopasowuje bieżący komputer przy użyciu symbolu wieloznacznego `'.*'`.
- `AppFilter='WebAppExclude'` zawiera klucz Instrumentacji `null`. Określona aplikacja nie będzie Instrumentacją.
- `AppFilter='WebAppOne'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- `AppFilter='WebAppTwo'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- Na koniec `AppFilter` używa również symbolu wieloznacznego `'.*'`, aby dopasować wszystkie aplikacje sieci Web, które nie są zgodne ze starszymi regułami i przypisać domyślny klucz Instrumentacji.
- Spacje są dodawane do czytelności.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby dostarczyć pojedynczy klucz instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru, aby podać wiele kluczy Instrumentacji i mapowanie kluczy Instrumentacji używanych przez poszczególne aplikacje.
Można utworzyć pojedynczy skrypt instalacyjny dla kilku komputerów, ustawiając `MachineFilter`.

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w jakiej są udostępniane reguły. Dlatego należy najpierw określić najbardziej szczegółowe reguły i najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** to wymagane C# wyrażenie regularne nazwy komputera lub maszyny wirtualnej.
    - ". *" będzie pasować do wszystkich
    - Nazwa "ComputerName" będzie pasować tylko do komputerów o określonej nazwie.
- **AppFilter** to wymagane C# wyrażenie regularne nazwy komputera lub maszyny wirtualnej.
    - ". *" będzie pasować do wszystkich
    - "ApplicationName" będzie pasować tylko do aplikacji IIS o określonej nazwie.
- **InstrumentationKey** jest wymagany do włączenia monitorowania aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły do wykluczenia monitorowania.


### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Użyj tego przełącznika, aby wyświetlić szczegółowe dzienniki.


## <a name="output"></a>Dane wyjściowe

Domyślnie Brak danych wyjściowych.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Przykład pełnych danych wyjściowych dotyczących ustawiania pliku konfiguracji za pomocą parametru-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Przykład pełnych danych wyjściowych dotyczących ustawiania pliku konfiguracji za pomocą parametru-InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Poznaj metryki](../../azure-monitor/app/metrics-explorer.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
- [Użyj analizy](../../azure-monitor/app/analytics.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Utwórz testy sieci Web](monitor-web-app-availability.md) , aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj zestaw Application Insights SDK do kodu](../../azure-monitor/app/asp-net.md) , aby móc wstawiać śledzenie i rejestrowanie wywołań
 
 Zrób więcej dzięki Application Insights agentowi:
 - Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) , aby upewnić się, że Twoje ustawienia zostały poprawnie zarejestrowane.
 - [Pobierz stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
