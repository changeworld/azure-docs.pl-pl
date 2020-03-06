---
title: Azure Government Monitorowanie + zarządzanie | Microsoft Docs
description: Zapewnia to porównanie funkcji i wskazówek dotyczących tworzenia aplikacji dla Azure Government.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362261"
---
# <a name="azure-government-monitoring--management"></a>Azure Government Monitorowanie + zarządzanie
W tym artykule opisano różnice i zagadnienia dotyczące usług monitorowania i zarządzania dla środowiska Azure Governmentowego.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Usługi Advisor są ogólnie dostępne w Azure Government.

Aby uzyskać więcej informacji, zobacz [publiczna Dokumentacja usługi Advisor](../advisor/advisor-overview.md).

### <a name="variations"></a>Obsługi
Poniższe zalecenia doradcy nie są obecnie dostępne w Azure Government:

* Wysoka dostępność
  * Skonfiguruj bramę sieci VPN do usługi Active-Active na potrzeby odporności połączeń
  * Utwórz alerty Azure Service Health, aby otrzymywać powiadomienia o problemach z platformą Azure
  * Skonfiguruj Traffic Manager punkty końcowe pod kątem odporności
  * Użyj usunięcia nietrwałego dla konta usługi Azure Storage
* Wydajność
  * Zwiększ wydajność i niezawodność App Service
  * Skracanie czasu usługi DNS na żywo w profilu Traffic Manager, aby szybciej pracować w trybie failover z prawidłowymi punktami końcowymi
  * Poprawianie wydajności SQL Data Warehouse
  * Korzystanie z magazynu Premium Storage
  * Migrowanie konta magazynu do Azure Resource Manager
* Koszty
  * Kupowanie wystąpień zarezerwowanych maszyn wirtualnych, aby zaoszczędzić pieniądze w porównaniu z kosztami płatność zgodnie z rzeczywistym użyciem
  * Usuń niezainicjowane obwody usługi ExpressRoute
  * Usuwanie lub ponowne konfigurowanie bezczynnych bram sieci wirtualnej

Obliczenia stosowane w celu zalecania odpowiedniego rozmiaru lub zamknięcia nieużywanych maszyn wirtualnych są Azure Government następujące:

Klasyfikator monitoruje użycie maszyn wirtualnych przez 7 dni i identyfikuje maszyny wirtualne o niskim obciążeniu. Maszyny wirtualne są uznawane za niskie wykorzystanie, jeśli ich użycie procesora CPU wynosi 5% lub mniej, a ich użycie sieci jest mniejsze niż 2% lub jeśli bieżące obciążenie może być dostosowane do mniejszego rozmiaru maszyny wirtualnej. Jeśli chcesz bardziej agresywnie identyfikować nieużywane maszyny wirtualne, możesz dostosować regułę użycia procesora CPU dla każdej subskrypcji.

## <a name="automation"></a>Automatyzacja
Automatyzacja jest ogólnie dostępna w Azure Government.

Aby uzyskać więcej informacji, zobacz [dokumentację publiczną usługi Automation](../automation/automation-intro.md).

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate jest ogólnie dostępna w Azure Government.

Aby uzyskać więcej informacji, zobacz [dokumentację Azure Migrate](../migrate/migrate-overview.md).

### <a name="variations"></a>Obsługi
Następujące funkcje Azure Migrate nie są obecnie dostępne w Azure Government:

* Funkcja wizualizacji zależności nie jest dostępna w Azure Government, ponieważ Azure Migrate zależy od Service Map dla wizualizacji zależności, która jest obecnie niedostępna w Azure Government.
* Możesz tworzyć tylko oceny dla Azure Government jako regiony docelowe i korzystając z ofert Azure Government.

## <a name="backup"></a>Backup
Kopia zapasowa jest ogólnie dostępna w Azure Government.

Aby uzyskać więcej informacji, zobacz [Azure Government Backup](documentation-government-services-backup.md).

## <a name="policy"></a>Zasady
Zasady są ogólnie dostępne w Azure Government.

Aby uzyskać więcej informacji, zobacz [Azure Policy](../governance/policy/overview.md).

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery jest ogólnie dostępna w Azure Government.

Aby uzyskać więcej informacji, zobacz [Site Recovery dokumentacji komercyjnej](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Obsługi
Następujące funkcje Site Recovery nie są obecnie dostępne w Azure Government:
* Powiadomienie e-mail

| Site Recovery | Wdrożenie klasyczne | Resource Manager |
| --- | --- | --- |
| Oprogramowanie VMware/Physical  | Ogólna dostępność | Ogólna dostępność |
| Funkcja Hyper-V | Ogólna dostępność | Ogólna dostępność |
| Lokacja-lokacja | Ogólna dostępność | Ogólna dostępność |

Następujące adresy URL dla Site Recovery są różne w Azure Government:

| Azure Public | Azure Government | Uwagi |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*. hypervrecoverymanager.windowsazure.us | Dostęp do usługi Site Recovery |
| \*.backup.windowsazure.com  | \*. backup.windowsazure.us | Dostęp do usługi ochrony |
| \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Do przechowywania migawek maszyn wirtualnych |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Aby pobrać MySQL |

## <a name="monitor"></a>Monitorowanie
Azure Monitor jest ogólnie dostępna w Azure Government.

Aby uzyskać więcej informacji, zobacz artykuł [monitorowanie komercyjnej dokumentacji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Obsługi
W poniższych sekcjach szczegółowo opisano różnice i Obejścia dotyczące funkcji Azure Monitor w programie Azure Government:

#### <a name="action-groups"></a>Grupy akcji
Grupy akcji są ogólnie dostępne w Azure Government bez różnic z komercyjnej platformy Azure.   

#### <a name="activity-log-alerts"></a>Alerty dziennika aktywności
Alerty dziennika aktywności są ogólnie dostępne w Azure Government bez różnic z komercyjnej platformy Azure.

#### <a name="alerts-experience"></a>Środowisko alertów
Interfejs użytkownika ujednoliconych alertów jest dostępny do obsługi alertów dotyczących metryk i dzienników w Azure Government.

#### <a name="autoscale"></a>Automatyczne skalowanie
Automatyczne skalowanie jest ogólnie dostępne w Azure Government.

Jeśli używasz wywołań PowerShell/ARM/REST, aby określić ustawienia, ustaw "lokalizację" skalowania automatycznego na "USGov Wirginia" lub "USGov Iowa". Zasób, którego dotyczy automatyczne skalowanie, może istnieć w dowolnym regionie. Poniżej znajduje się przykład ustawienia:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Jeśli interesuje Cię zaimplementowanie automatycznego skalowania zasobów, użyj wywołań programu PowerShell/ARM/REST, aby określić ustawienia.

Aby uzyskać więcej informacji na temat korzystania z programu PowerShell, zobacz [publiczna dokumentacja](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Metryki
Metryki są ogólnie dostępne w Azure Government. Jednak metryki wielowymiarowe są obsługiwane tylko za pośrednictwem interfejsu API REST. Możliwość [wyświetlania metryk wielowymiarowych](../azure-monitor/platform/metrics-charts.md) jest w wersji zapoznawczej w portalu Azure Government.

#### <a name="metric-alerts"></a>Alerty metryki
Pierwsze generowanie alertów metryk jest ogólnie dostępne zarówno w Azure Government, jak i na platformie Azure. Pierwsza generacja jest nazywana *alertami (klasyczne)* . Druga generacja alertów dotyczących metryk (nazywana również działaniem [ujednoliconych alertów](../azure-monitor/platform/alerts-overview.md)) jest teraz również dostępna, ale ze zredukowanym zestawem dostawców zasobów w [porównaniu z chmurą publiczną](../azure-monitor/platform/alerts-metric-near-real-time.md). Więcej zostanie dodany z upływem czasu. 

Obecnie obsługiwane są zasoby w ramach alertów drugiej generacji:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft. EventGrid/domeny
- Microsoft. EventGrid/tematy
- Microsoft. EventHub/klastry
- Microsoft.EventHub/namespaces
- Microsoft. Insights/składniki
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft. Relay/przestrzenie nazw
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft. Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

Nadal można używać [klasycznych alertów](../azure-monitor/platform/alerts-classic.overview.md) dla zasobów, które nie są jeszcze dostępne w drugiej generacji alertów. 

W przypadku używania wywołań programu PowerShell/ARM/REST w celu tworzenia alertów metryk należy ustawić "lokalizację" alertu metryki na "USGov Wirginia" lub "USGov Iowa". Poniżej znajduje się przykład ustawienia:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Aby uzyskać więcej informacji na temat korzystania z programu PowerShell, zobacz [publiczna dokumentacja](../azure-monitor/platform/powershell-quickstart-samples.md).

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Monitorowanie oparte na agentach i rozszerzeniach niezwiązanych z kodem dla platformy Azure App Services **nie jest obecnie obsługiwane**. Po udostępnieniu tej funkcji ten artykuł zostanie zaktualizowany.

W tej sekcji opisano dodatkową konfigurację, która jest wymagana do używania Application Insights w programie Azure Government. Aby dowiedzieć się więcej na temat Azure Monitor i Application Insights wyewidencjonować [pełną dokumentację](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Włączanie Application Insights ASP.NET & ASP.NET Core z programem Visual Studio

Obecnie w przypadku Azure Government klientów jedynym sposobem włączania Application Insights za pomocą tradycyjnego przycisku **Dodaj telemetrię usługi Application Insights** w programie Visual Studio jest wymaganie małego obejścia ręcznego. Klienci z odpowiednim problemem mogą zobaczyć komunikaty o błędach, takie jak _"nie ma subskrypcji platformy Azure skojarzonej z tym kontem_ " lub _"wybrana subskrypcja nie obsługuje Application Insights_ , mimo że dostawca zasobów `microsoft.insights` ma stan zarejestrowano dla subskrypcji. Aby rozwiązać ten problem, należy wykonać następujące czynności:

1. Przełącz program Visual Studio [, aby kierować chmurą Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Utwórz (lub jeśli już istnieje) zmienna środowiskowa użytkownika dla AzureGraphApiVersion w następujący sposób: (aby utworzyć zmienną środowiskową użytkownika przejdź do **Panelu sterowania** > **system** > **Zaawansowane ustawienia systemu** > **Zaawansowane** **zmienne środowiskowe** > .)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Wprowadź odpowiednie modyfikacje punktu końcowego Application Insights zestawu SDK dla [ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) lub [ASP.NET Core](#aspnet-core) w zależności od typu projektu.

### <a name="snapshot-debugger"></a>Debuger migawek

Snapshot Debugger jest teraz dostępna dla Azure Government klientów. Aby użyć Snapshot Debugger jedynym dodatkowym wymaganiem wstępnym jest upewnienie się, że używasz [Snapshot Collector w wersji 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) lub nowszej. Następnie po prostu postępuj zgodnie ze standardową [dokumentacją Snapshot Debugger](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>Modyfikacje punktu końcowego zestawu SDK

Aby można było wysyłać dane z Application Insights do regionu Azure Government, należy zmodyfikować domyślne adresy punktów końcowych, które są używane przez zestawy SDK Application Insights. Każdy zestaw SDK wymaga nieco innych modyfikacji.

### <a name="net-with-applicationinsightsconfig"></a>.NET z ApplicationInsights. config

> [!NOTE]
> Plik ApplicationInsights. config jest automatycznie zastępowany podczas uaktualniania zestawu SDK. Po uaktualnieniu zestawu SDK Pamiętaj o ponownym wprowadzeniu wartości punktu końcowego specyficznego dla regionu.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Zmodyfikuj plik appSettings. JSON w projekcie w następujący sposób, aby dostosować główny punkt końcowy:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Wartości metryk na żywo i punkt końcowy zapytania profilu można ustawić tylko za pośrednictwem kodu. Aby zastąpić wartości domyślne dla wszystkich wartości punktów końcowych za pośrednictwem kodu, wprowadź następujące zmiany w metodzie `ConfigureServices` pliku `Startup.cs`:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

Zainstaluj następujące pakiety w projekcie funkcji:

- Microsoft. ApplicationInsights w wersji 2.10.0
- Microsoft. ApplicationInsights. PerfCounterCollector w wersji 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel, wersja 2.10.0

A także dodawać (lub modyfikować) kod uruchamiania aplikacji funkcji:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Zmodyfikuj plik ApplicationInsights. XML, aby zmienić domyślny adres punktu końcowego.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Zmodyfikuj plik `application.properties` i Dodaj:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Punkty końcowe można również skonfigurować za pomocą zmiennych środowiskowych:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Wyjątki zapory

Usługa Azure Application Insights używa wielu adresów IP. Może być konieczne poznanie tych adresów, jeśli monitorowana aplikacja jest hostowana za zaporą.

> [!NOTE]
> Chociaż te adresy są statyczne, istnieje możliwość, że będziemy musieli zmienić je od czasu do czasu. Cały ruch Application Insights reprezentuje ruch wychodzący z wyjątkiem monitorowania dostępności i elementów webhook, które wymagają reguł zapory dla ruchu przychodzącego.

### <a name="outgoing-ports"></a>Porty wychodzące
Należy otworzyć niektóre porty wychodzące w zaporze serwera, aby zezwolić Application Insights SDK i/lub monitor stanu na wysyłanie danych do portalu:

| Przeznaczenie | Adres URL | Adres IP | Porty |
| --- | --- | --- | --- |
| Telemetria | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
Dzienniki Azure Monitor są ogólnie dostępne w Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Obsługi

* Rozwiązania, które są dostępne w Azure Government obejmują:
  * [Network Performance Monitor (npm)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) — npm to oparte na chmurze rozwiązanie do monitorowania sieci dla publicznych i hybrydowych środowisk chmurowych. Organizacje używają NPM do monitorowania dostępności sieci w środowiskach lokalnych i w chmurze.  Monitor punktu końcowego — podfunkcja NPM, monitorująca łączność sieciową z aplikacjami.

Następujące Azure Monitor dzienniki funkcji i rozwiązań nie są obecnie dostępne w programie Azure Government.

* Rozwiązania, które są dostępne w wersji zapoznawczej w Microsoft Azure, w tym:
  * Mapa usługi
  * Rozwiązanie Upgrade Analytics systemu Windows 10
  * Application Insights rozwiązanie
  * Rozwiązanie do analizy grup zabezpieczeń sieci platformy Azure
  * Rozwiązanie Azure Automation Analytics
  * Key Vault Analytics rozwiązanie
* Rozwiązania i funkcje, które wymagają aktualizacji oprogramowania lokalnego, w tym:
  * Surface Hub rozwiązanie
* Funkcje, które są w wersji zapoznawczej na globalnym platformie Azure, w tym:
  * Eksportowanie danych do Power BI
* Metryki platformy Azure i Diagnostyka platformy Azure

Adresy URL dzienników Azure Monitor są różne w Azure Government:

| Azure Public | Azure Government | Uwagi |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portal obszarów roboczych Log Analytics |
| *Identyfikator obszaru roboczego*. ods.OpInsights.Azure.com |*Identyfikator obszaru roboczego*. ods.OpInsights.Azure.us |[Interfejs API modułu zbierającego dane](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*. ods.opinsights.azure.us |Komunikacja z agentem — [Konfigurowanie ustawień zapory](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*. oms.opinsights.azure.us |Komunikacja z agentem — [Konfigurowanie ustawień zapory](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*. blob.core.usgovcloudapi.net |Komunikacja z agentem — [Konfigurowanie ustawień zapory](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Portal analizy zaawansowanej — [Konfigurowanie ustawień zapory](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Portal analizy zaawansowanej — [Konfigurowanie ustawień zapory](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Portal analizy zaawansowanej — [Konfigurowanie ustawień zapory](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Azure Automation — [Konfigurowanie ustawień zapory](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| Nie dotyczy | *. usgovtrafficmanager.net | Traffic Manager platformy Azure — [Konfigurowanie ustawień zapory](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

Następujące Azure Monitor funkcje dzienników działają inaczej w Azure Government:

* Aby połączyć System Center Operations Manager grupę zarządzania z dziennikami Azure Monitor, należy pobrać i zaimportować zaktualizowane pakiety administracyjne.
  + System Center Operations Manager 2016
    1. Zainstaluj [pakiet zbiorczy aktualizacji 2 dla System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Zaimportuj pakiety administracyjne zawarte w ramach pakietu zbiorczego aktualizacji 2 do Operations Manager. Informacje o sposobach importowania pakietu administracyjnego z dysku znajdują się w temacie [jak zaimportować pakiet administracyjny Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    3. Aby połączyć Operations Manager z dziennikami Azure Monitor, wykonaj kroki opisane w sekcji [łączenie Operations Manager z dziennikami Azure monitor](../azure-monitor/platform/om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (lub nowszy)/Operations Manager 2012 SP1 pakietem UR7 (lub nowszy)
    1. Pobierz i Zapisz [zaktualizowane pakiety administracyjne](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Rozpakuj pobrany plik.
    3. Zaimportuj pakiety administracyjne do Operations Manager. Informacje o sposobach importowania pakietu administracyjnego z dysku znajdują się w temacie [jak zaimportować pakiet administracyjny Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    4. Aby połączyć Operations Manager z dziennikami Azure Monitor, wykonaj kroki opisane w sekcji [łączenie Operations Manager z dziennikami Azure monitor](../azure-monitor/platform/om-agents.md).

* Aby uzyskać więcej informacji o korzystaniu z grup komputerów z Configuration Manager, zobacz [Connect Configuration Manager to Azure monitor](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Często zadawane pytania
* Czy można migrować dane z dzienników Azure Monitor w Microsoft Azure do Azure Government?
  * Nie. Przenoszenie danych lub obszaru roboczego z Microsoft Azure do Azure Government nie jest możliwe.
* Czy mogę przełączać się między obszarami roboczymi Microsoft Azure i Azure Government z portalu Operations Management Suite?
  * Nie. Portale dla Microsoft Azure i Azure Government są oddzielne i nie udostępniają informacji.

Aby uzyskać więcej informacji, zobacz [Azure monitor dziennika publicznego](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Harmonogram
Aby uzyskać informacje na temat tej usługi i sposobu korzystania z niej, zobacz [dokumentację usługi Azure Scheduler](../scheduler/index.md).

## <a name="azure-portal"></a>Portalu Azure
Dostęp do portalu Azure Government można uzyskać [tutaj](https://portal.azure.us).

## <a name="azure-resource-manager"></a>Azure Resource Manager
Aby uzyskać informacje na temat tej usługi i sposobu korzystania z niej, zobacz [dokumentację Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>Następne kroki
* Subskrybowanie [blogu Azure Government](https://blogs.msdn.microsoft.com/azuregov/)
* Uzyskaj pomoc dotyczącą Stack Overflow przy użyciu [platformy Azure — gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Przekaż opinię lub zażądaj nowych funkcji za pośrednictwem [forum opinii Azure Government](https://feedback.azure.com/forums/558487-azure-government)
