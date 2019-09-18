---
title: 'Dokumentacja interfejsu API platformy Azure monitor stanu v2: Pobierz stan | Microsoft Docs'
description: Dokumentacja interfejsu API monitor stanu v2. Get-ApplicationInsightsMonitoringStatus. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: c3982e7eb78c1113c73a8e7e9d7b00ad403ac486
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058254"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus"></a>Interfejs API monitor stanu v2: Get-ApplicationInsightsMonitoringStatus

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

To polecenie cmdlet zawiera informacje dotyczące rozwiązywania problemów dotyczących monitor stanu.
Użyj tego polecenia cmdlet, aby sprawdzić stan monitorowania, wersję modułu programu PowerShell i sprawdzić uruchomiony proces.
To polecenie cmdlet będzie zgłaszać informacje o wersji i informacje o plikach kluczy wymaganych do monitorowania.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

### <a name="example-application-status"></a>Przykład: Stan aplikacji

Uruchom polecenie `Get-ApplicationInsightsMonitoringStatus` , aby wyświetlić stan monitorowania witryn sieci Web.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

W tym przykładzie:
- **Identyfikator maszyny** to anonimowy identyfikator używany do jednoznacznego identyfikowania Twojego serwera. Jeśli utworzysz żądanie pomocy technicznej, będziemy potrzebować tego identyfikatora, aby znaleźć dzienniki dla Twojego serwera.
- **Domyślna witryna sieci Web** jest zatrzymana w usługach IIS
- **DemoWebApp111** został uruchomiony w usługach IIS, ale nie otrzymał żadnych żądań. Ten raport pokazuje, że nie ma uruchomionego procesu (identyfikator procesu: nie znaleziono).
- **DemoWebApp222** działa i jest monitorowany (Instrumentacja: true). W oparciu o konfigurację użytkownika klucz Instrumentacji xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 został dopasowany do tej witryny.
- **DemoWebApp333** został ręcznie Instrumentacja przy użyciu zestawu SDK Application Insights. Monitor stanu wykrył zestaw SDK i nie będzie monitorował tej lokacji.


### <a name="example-powershell-module-information"></a>Przykład: Informacje o module programu PowerShell

Uruchom polecenie `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` , aby wyświetlić informacje o bieżącym module:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

### <a name="example-runtime-status"></a>Przykład: Stan środowiska uruchomieniowego

Możesz sprawdzić proces na komputerze z instrumentacją, aby sprawdzić, czy wszystkie biblioteki DLL zostały załadowane. Jeśli monitorowanie działa, należy załadować co najmniej 12 bibliotek DLL.

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

Domyślnie to polecenie cmdlet będzie zgłaszać stan monitorowania aplikacji sieci Web.
Użyj tej opcji, aby sprawdzić, czy aplikacja została pomyślnie przeprowadzona.
Możesz również sprawdzić, który klucz Instrumentacji został dopasowany do witryny.


### <a name="-powershellmodule"></a>-PowerShellModule
**Opcjonalnie**. Użyj tego przełącznika, aby zgłosić numery wersji i ścieżki bibliotek DLL wymaganych do monitorowania.
Użyj tej opcji, jeśli konieczne jest zidentyfikowanie wersji dowolnej biblioteki DLL, w tym zestawu SDK Application Insights.

### <a name="-inspectprocess"></a>-InspectProcess

**Opcjonalnie**. Użyj tego przełącznika, aby zgłosić, czy usługi IIS są uruchomione.
Zostaną również pobrane zewnętrzne narzędzia, aby określić, czy wymagane biblioteki DLL są ładowane do środowiska uruchomieniowego usług IIS.


Jeśli ten proces nie powiedzie się z jakiegokolwiek powodu, można uruchomić następujące polecenia ręcznie:
- iisreset. exe/status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Opcjonalnie**. Używane tylko z InspectProcess. Użyj tego przełącznika, aby pominąć monit użytkownika, który pojawia się przed pobraniem dodatkowych narzędzi.


## <a name="next-steps"></a>Następne kroki

 Więcej informacji o monitor stanu v2:
 - Skorzystaj z naszego przewodnika, aby [rozwiązywać problemy z](status-monitor-v2-troubleshoot.md) Monitor stanu v2.
