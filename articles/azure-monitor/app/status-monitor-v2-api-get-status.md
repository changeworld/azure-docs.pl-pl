---
title: 'Dokumentacja interfejsu API w wersji 2 usługi Azure Monitor stanu: Pobierz stan | Dokumentacja firmy Microsoft'
description: Dokumentacja interfejsu API w wersji 2 Monitor stanu. Get-ApplicationInsightsMonitoringStatus. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: 860226320fe1a546798cc462e4e5c06d4b9228cf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514303"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v022-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Get-ApplicationInsightsMonitoringStatus (v0.2.2-alpha)

W tym artykule opisano polecenia cmdlet, które jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Opis

Umożliwia Rozwiązywanie problemów z modułu programu PowerShell, który jest używany.
To polecenie cmdlet będzie zgłaszać informacje o wersji i informacji o plikach kluczy, wymaganych na potrzeby monitorowania.
Dodatkowe parametry zapewniają dodatkowe raporty dotyczące stanu monitorowania.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady


### <a name="example-basic-information"></a>Przykład: Podstawowe informacje

Uruchom `Get-ApplicationInsightsMonitoringStatus` do wyświetlania informacji na temat bieżącego modułu:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus

PowerShell Module version:
0.2.2-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content Exists: False
ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\applicationInsights.ikey.config Exists: True
ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True


Machine Identifier:
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF
```

### <a name="example-runtime-status"></a>Przykład: Stan czasu wykonywania

Można sprawdzić procesu na komputerze instrumentowane, aby sprawdzić, czy wszystkie biblioteki DLL są ładowane. Jeśli działa monitorowania, co najmniej 12 biblioteki DLL powinny być załadowane.

Uruchom polecenie `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>Parametry

### <a name="no-parameters"></a>(Brak parametrów)

Domyślnie to polecenie cmdlet będzie zgłaszać numery wersji i ścieżki biblioteki DLL wymaganych na potrzeby monitorowania.

Użyj tej opcji, jeśli potrzebujesz do identyfikowania wersji dowolną biblioteką DLL, w tym zestaw SDK usługi Application Insights.


### <a name="-inspectprocess"></a>-InspectProcess

**Opcjonalnie**. Użyj tego parametru, aby zgłosić, czy usługi IIS są uruchomione.
Również pobierze zewnętrznych narzędzi, aby ustalić, czy odpowiednie biblioteki DLL są ładowane w czasie wykonywania usług IIS.


Jeśli ten proces nie powiedzie się z jakiegokolwiek powodu, możesz ręcznie uruchom następujące polecenia:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) w3wp -p | findstr /I "InstrumentationEngine sztucznej Inteligencji. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Opcjonalnie**. Używany tylko z InspectProcess. Aby pominąć monit użytkownika, który pojawia się przed są pobierane dodatkowe narzędzia, użyj tego przełącznika.


## <a name="next-steps"></a>Kolejne kroki

 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
