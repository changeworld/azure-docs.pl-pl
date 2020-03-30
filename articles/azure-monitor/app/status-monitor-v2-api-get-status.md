---
title: Odwołanie do interfejsu API agenta usługi Azure Application Insights
description: Odwołanie do interfejsu API agenta usługi Application Insights. Get-ApplicationInsightsMonitoringStatus. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 159dab4a228c822ef62c45c9ccceff638a9bea45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671260"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Interfejs API agenta usługi Application Insights: Get-ApplicationInsightsMonitoringStatus

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

To polecenie cmdlet zawiera informacje dotyczące rozwiązywania problemów z Monitorem stanu.
Użyj tego polecenia cmdlet, aby zbadać stan monitorowania, wersję modułu programu PowerShell i sprawdzić uruchomiony proces.
To polecenie cmdlet będzie raportował informacje o wersji i informacje o kluczowych plikach wymaganych do monitorowania.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

### <a name="example-application-status"></a>Przykład: Stan aplikacji

Uruchom polecenie, `Get-ApplicationInsightsMonitoringStatus` aby wyświetlić stan monitorowania witryn sieci web.

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

W tym przykładzie;
- **Identyfikator maszyny** to anonimowy identyfikator używany do jednoznacznej identyfikacji serwera. Jeśli utworzysz żądanie pomocy technicznej, ten identyfikator będzie potrzebny do znajdowania dzienników serwera.
- **Domyślna witryna sieci Web** została zatrzymana w usługach IIS
- **DemoWebApp111** został uruchomiony w uiświadach iis, ale nie otrzymał żadnych żądań. Ten raport pokazuje, że nie ma uruchomionego procesu (ProcessId: nie znaleziono).
- **DemoWebApp222** jest uruchomiony i jest monitorowany (Instrumented: true). Na podstawie konfiguracji użytkownika, Instrumentation Key xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 został dopasowany do tej strony.
- **DemoWebApp333** został ręcznie instrumentowany przy użyciu SDK usługi Application Insights. Monitor stanu wykrył sdk i nie będzie monitorował tej lokacji.


### <a name="example-powershell-module-information"></a>Przykład: Informacje o module programu PowerShell

Uruchom polecenie, `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` aby wyświetlić informacje o bieżącym module:

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

Można sprawdzić proces na komputerze oprzyrządowanym, aby sprawdzić, czy wszystkie biblioteki DLL są ładowane. Jeśli monitorowanie działa, należy załadować co najmniej 12 bibliotek DLL.

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

Domyślnie to polecenie cmdlet będzie raportował stan monitorowania aplikacji sieci web.
Użyj tej opcji, aby przejrzeć, jeśli aplikacja została pomyślnie instrumentowane.
Możesz również przejrzeć, który klucz instrumentacji został dopasowany do Twojej witryny.


### <a name="-powershellmodule"></a>-PowerShellModule
**Opcjonalnie**. Ten przełącznik służy do raportowania numerów wersji i ścieżek bibliotek DLL wymaganych do monitorowania.
Użyj tej opcji, jeśli chcesz zidentyfikować wersję dowolnej biblioteki DLL, w tym zestaw SDK usługi Application Insights.

### <a name="-inspectprocess"></a>-InspectProcess

**Opcjonalnie**. Ten przełącznik służy do raportowania, czy iIS jest uruchomiony.
Pobierze również narzędzia zewnętrzne, aby ustalić, czy niezbędne biblioteki DLL są ładowane do środowiska wykonawczego IIS.


Jeśli ten proces nie powiedzie się z jakiegokolwiek powodu, można uruchomić te polecenia ręcznie:
- iisreset.exe /status
- [uchwyt64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Opcjonalnie**. Używany tylko z InspectProcess. Użyj tego przełącznika, aby pominąć monit użytkownika, który pojawia się przed pobraniem dodatkowych narzędzi.


## <a name="next-steps"></a>Następne kroki

 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
