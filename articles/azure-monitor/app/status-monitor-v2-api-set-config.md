---
title: 'Dokumentacja interfejsu API w wersji 2 usługi Azure Monitor stanu: Ustawianie konfiguracji | Dokumentacja firmy Microsoft'
description: Dokumentacja interfejsu API w wersji 2 Monitor stanu. Set-ApplicationInsightsMonitoringConfig. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami sieci web platformy ASP.NET hostowanej lokalnie, na maszynach wirtualnych lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: e63d935b3c11766c4981ffb035dd45ad4019797c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807068"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v040-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Set-ApplicationInsightsMonitoringConfig (v0.4.0-alpha)

W tym dokumencie opisano polecenia cmdlet, które jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Opis

Ustawia plik konfiguracji bez wykonania tej operacji ponownej instalacji.
Ponowne uruchomienie usług IIS, aby zmiany zaczęły obowiązywać.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.


## <a name="examples"></a>Przykłady

### <a name="example-with-a-single-instrumentation-key"></a>Przykład: klucz Instrumentacji pojedynczego
W tym przykładzie wszystkie aplikacje na bieżącym komputerze zostanie przypisany klucz Instrumentacji jednego.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą klucza Instrumentacji
W tym przykładzie:
- `MachineFilter` Dopasowuje bieżący komputer przy użyciu `'.*'` symboli wieloznacznych.
- `AppFilter='WebAppExclude'` udostępnia `null` klucz instrumentacji. Określona aplikacja nie będzie instrumentowany.
- `AppFilter='WebAppOne'` przypisuje określona aplikacja klucz Instrumentacji unikatowy.
- `AppFilter='WebAppTwo'` przypisuje określona aplikacja klucz Instrumentacji unikatowy.
- Na koniec `AppFilter` używa również `'.*'` symboli wieloznacznych, aby dopasować wszystkie aplikacje sieci web, które nie zostały dopasowane przez wcześniejsze reguły i przypisz domyślny klucz instrumentacji.
- Miejsca do magazynowania są dodawane dla czytelności.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby podać klucz Instrumentacji pojedynczej do użytku przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru jest podanie wielu kluczy Instrumentacji i mapowanie kluczy instrumentacji, używanych przez każdą aplikację.
Możesz utworzyć skrypt jednej instalacji dla kilku komputerów, ustawiając `MachineFilter`.

> [!IMPORTANT]
> Aplikacje zostanie dopasowany do reguły w kolejności, które zasady zostały udostępnione. Dlatego najpierw należy określić najbardziej specyficzne reguły, a ostatni najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** jest wymaganym C# wyrażeń regularnych, komputera lub nazwa maszyny Wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - 'ComputerName' będzie zgodna tylko komputery z określoną nazwą.
- **AppFilter** jest wymaganym C# wyrażeń regularnych, komputera lub nazwa maszyny Wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - "ApplicationName" będzie zgodna tylko aplikacje usługi IIS przy użyciu określonej nazwy.
- **InstrumentationKey** jest wymagane do umożliwienia monitorowania aplikacji, które odpowiadają poprzednim dwa filtry.
    - Pozostaw tę wartość na wartość null, jeśli chcesz zdefiniować zasady, które mają zostać wykluczone z monitorowania.


### <a name="-verbose"></a>-Verbose
**Typowy parametr.** Aby wyświetlić szczegółowe dzienniki, należy użyć tego przełącznika.


## <a name="output"></a>Output

Domyślnie żadne dane wyjściowe.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Przykład pełne dane wyjściowe z ustawień pliku konfiguracji, za pośrednictwem elementu InstrumentationKey-

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Przykład pełne dane wyjściowe z ustawień pliku konfiguracji, za pośrednictwem - InstrumentationKeyMap

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
 - [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) do monitorowania wydajności i użycia.
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów.
- [Korzystanie z analizy](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wywołania śledzenia.
- [Dodaj zestaw Application Insights SDK do kodu](../../azure-monitor/app/asp-net.md) dzięki czemu można wstawić ślad i rejestrować wywołania
 
 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) aby upewnić się, że Twoje ustawienia zostały prawidłowo zarejestrowane.
 - [Pobierz stan](status-monitor-v2-api-get-status.md) do inspekcji, monitorowania.
