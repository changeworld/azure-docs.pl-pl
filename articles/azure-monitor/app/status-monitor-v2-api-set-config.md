---
title: 'Dokumentacja interfejsu API w wersji 2 Monitora stanu platformy Azure: Ustawianie konfiguracji | Dokumentacja firmy Microsoft'
description: Stan monitora v2 interfejsu API odwołanie do zestawu ApplicationInsightsMonitoringConfig. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: 971fea76a23859f32437a1698b6d3094113737a1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255094"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

W tym dokumencie opisano polecenia cmdlet, który jest dostarczany jako członek [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Opis

Ustawienie pliku konfiguracji bez powtarzania pełna ponowna instalacja. Ponowne uruchomienie usług IIS, aby zmiany zaczęły obowiązywać.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.


## <a name="examples"></a>Przykłady

### <a name="example-with-single-instrumentation-key"></a>Przykład: klucz Instrumentacji pojedynczego
W tym przykładzie wszystkie aplikacje na bieżącym komputerze zostanie przypisany klucz Instrumentacji jednego.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Przykład: mapy kluczy Instrumentacji
W tym przykładzie 
- `MachineFilter` będą pasować do bieżącego komputera przy użyciu `'.*'` symboli wieloznacznych.
- `AppFilter='WebAppExclude'` udostępnia `null` elementu InstrumentationKey. Ta aplikacja nie będzie instrumentowany.
- `AppFilter='WebAppOne'` przypisze tej konkretnej aplikacji klucz Instrumentacji unikatowy.
- `AppFilter='WebAppTwo'` również spowoduje przypisanie klucza Instrumentacji unikatowy tej konkretnej aplikacji.
- Na koniec `AppFilter` używa również `'.*'` symboli wieloznacznych, aby dopasować wszystkie pozostałe aplikacje sieci web dopasowane przez wcześniejsze reguły i przypisuje domyślny klucz instrumentacji.
- Dodano dla czytelności tylko miejsca do magazynowania.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametry 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby dostarczyć pojedynczego klucza iKey do użytku przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru, aby podać wiele kluczy ikey i mapowanie aplikacje, które do użycia, które klucz instrumentacji. Ustawiając MachineFilter, możesz utworzyć skrypt jednej instalacji na kilku komputerach. 

> [!IMPORTANT] 
> Aplikacje zostanie dopasowany do reguły w kolejności, że są one udostępniane. Dlatego najpierw należy określić najbardziej specyficzne reguły, a ostatni najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** jest regex wymagane c# o nazwie komputera lub maszyny wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - 'ComputerName' będzie zgodna tylko komputery z takiej samej nazwie.
- **AppFilter** jest regex wymagane c# o nazwie komputera lub maszyny wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - "ApplicationName" będzie zgodna tylko aplikacje usług IIS przy użyciu takiej samej nazwie.
- **InstrumentationKey** jest wymagane do umożliwienia monitorowania aplikacji, które pasują do powyższych dwóch filtrów.
    - Pozostaw tę wartość na wartość null, jeśli chcesz zdefiniować zasady, które mają zostać wykluczone z monitorowania


### <a name="-verbose"></a>-Verbose
**Typowy parametr.** W danych wyjściowych szczegółowych dzienników, należy użyć tego przełącznika.


## <a name="output"></a>Dane wyjściowe

Brak danych wyjściowych domyślnie.

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

## <a name="next-steps"></a>Kolejne kroki

  Wyświetlanie telemetrii:
 - [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) w celu monitorowania wydajności i użycia
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów
- [Analiza](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md)
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wstawianie wywołań śladu.
- [Dodawanie zestawu SDK usługi Application Insights do kodu](../../azure-monitor/app/asp-net.md) tak, aby wstawić ślad i rejestrować wywołania
 
 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [rozwiązywanie](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) aby upewnić się, że Twoje ustawienia zostały prawidłowo zarejestrowane.
 - [Pobierz stan](status-monitor-v2-api-get-status.md) do inspekcji, monitorowania.
