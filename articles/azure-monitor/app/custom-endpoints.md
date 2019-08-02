---
title: Azure Monitor — Application Insights platformy Azure przesłonić domyślne punkty końcowe zestawu SDK | Microsoft Docs
description: Zmodyfikuj domyślne punkty końcowe zestawu SDK usługi Azure Application Insights dla regionów takich jak Azure Government.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: mbullwin
ms.openlocfilehash: 662594f3ef0e4d5598b52cb07006b513e7f3c35a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564970"
---
 # <a name="application-insights-overriding-default-endpoints"></a>Application Insights Przesłanianie domyślnych punktów końcowych

Aby wysłać dane z Application Insights do określonych regionów, należy zastąpić domyślne adresy punktów końcowych. Każdy zestaw SDK wymaga nieco różnych modyfikacji, które zostały opisane w tym artykule. Te zmiany wymagają dostosowania przykładowego kodu i zastąpienia wartości zastępczych dla `QuickPulse_Endpoint_Address`, `TelemetryChannel_Endpoint_Address`i `Profile_Query_Endpoint_address` z rzeczywistymi adresami punktów końcowych dla danego regionu. Koniec tego artykułu zawiera linki do adresów punktów końcowych dla regionów, w których ta konfiguracja jest wymagana.

## <a name="sdk-code-changes"></a>Zmiany kodu zestawu SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET z ApplicationInsights. config

> [!NOTE]
> Plik ApplicationInsights. config jest automatycznie zastępowany podczas uaktualniania zestawu SDK. Po uaktualnieniu zestawu SDK Pamiętaj o ponownym wprowadzeniu wartości punktu końcowego specyficznego dla regionu.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="net-core"></a>.NET Core

Zmodyfikuj plik appSettings. JSON w projekcie w następujący sposób, aby dostosować główny punkt końcowy:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Wartości metryk na żywo i punkt końcowy zapytania profilu można ustawić tylko za pośrednictwem kodu. Aby zastąpić wartości domyślne dla wszystkich wartości punktów końcowych za pośrednictwem kodu, wprowadź następujące zmiany `ConfigureServices` w metodzie `Startup.cs` pliku:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
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
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

`application.properties` Zmodyfikuj plik i Dodaj:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Punkty końcowe można również skonfigurować za pomocą zmiennych środowiskowych:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"INSTRUMENTATION_KEY"
      endpointUrl: "TelemetryChannel_Endpoint_Address"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Regiony wymagające modyfikacji punktu końcowego

Obecnie jedyne regiony, które wymagają modyfikacji punktów końcowych, to [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) i [Chiny platformy Azure](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Region |  Nazwa punktu końcowego | Value |
|-----------------|:------------|:-------------|
| Azure — Chiny | Kanał telemetrii | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure — Chiny | QuickPulse (metryki na żywo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure — Chiny | Zapytanie profilu |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Kanał telemetrii |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (metryki na żywo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Zapytanie profilu |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Jeśli obecnie używasz [interfejsu API](https://dev.applicationinsights.io/
) REST Application Insights, do którego zwykle uzyskuje się dostęp za pośrednictwem elementu "API.ApplicationInsights.IO", musisz użyć punktu końcowego, który jest lokalny dla regionu:

|Region |  Nazwa punktu końcowego | Wartość |
|-----------------|:------------|:-------------|
| Azure — Chiny | Interfejs API REST | `api.applicationinsights.azure.cn` |
| Azure Government | Interfejs API REST | `api.applicationinsights.us`|

> [!NOTE]
> W tych regionach **nie jest obecnie obsługiwane** monitorowanie oparte na agentach i rozszerzeniach w ramach agentów usługi Azure App Services. Po udostępnieniu tej funkcji ten artykuł zostanie zaktualizowany.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat modyfikacji niestandardowych Azure Government, zapoznaj się ze szczegółowymi wskazówkami dotyczącymi [monitorowania i zarządzania platformą Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Aby dowiedzieć się więcej o Chinach platformy Azure, zapoznaj się z [element playbookem platformy Azure w Chinach](https://docs.microsoft.com/azure/china/).