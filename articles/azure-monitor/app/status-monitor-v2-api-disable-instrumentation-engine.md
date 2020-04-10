---
title: Instrumacja agenta usługi Azure Application InsightsEngine
description: Odwołanie do interfejsu API agenta usługi Application Insights. In disable-InstrumentationEngine. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998384"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Interfejs API agenta usługi Application Insights: In disable-InstrumentationEngine

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis
Wyłącza aparat instrumentacji, usuwając niektóre klucze rejestru.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry 

### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Użyj tego przełącznika do wyprowadzania szczegółowych dzienników.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Przykładowy wynik z pomyślnego wyłączenia silnika oprzyrządowania

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Następne kroki

 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
