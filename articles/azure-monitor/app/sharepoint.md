---
title: Monitorowanie witryny programu SharePoint za pomocą usługi Application Insights
description: Rozpocznij monitorowanie nowej aplikacji przy użyciu nowego klucza instrumentacji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: mbullwin
ms.openlocfilehash: 027d5485b29deb434953470023d8a290cc0af58a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784602"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorowanie witryny programu SharePoint za pomocą usługi Application Insights
Usługa Azure Application Insights monitoruje dostępność, wydajność i użycie Twoich aplikacji. Tutaj dowiesz się, jak skonfigurować je dla witryny programu SharePoint.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
W witrynie [Azure Portal](https://portal.azure.com) utwórz nowy zasób usługi Application Insights. Wybierz ASP.NET jako typ aplikacji.

![Kliknij opcję Właściwości, zaznacz klucz i naciśnij klawisze Ctrl+C](./media/sharepoint/001.png)

Okno, które zostanie otwarte, to miejsce, gdzie zobaczysz dane o wydajności i użyciu aplikacji. Aby wrócić do niego przy następnym logowaniu do platformy Azure, musisz znaleźć odpowiedni kafelek na ekranie startowym. Alternatywnie kliknij przycisk Przeglądaj, aby go znaleźć.

## <a name="add-the-script-to-your-web-pages"></a>Dodawanie skryptu do stron internetowych

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Wstaw skrypt tuż przed tagiem &lt;/head&gt; na każdej stronie, którą chcesz śledzić. Jeśli witryna ma stronę wzorcową, możesz umieścić skrypt na tej stronie. Na przykład w projekcie MVC programu ASP.NET możesz go umieścić w pliku View\Shared\_Layout.cshtml

Skrypt zawiera klucz instrumentacji, który kieruje dane telemetryczne do zasobu usługi Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Dodawanie kodu do stron witryny
#### <a name="on-the-master-page"></a>Na stronie wzorcowej
Jeśli możesz edytować stronę wzorcową witryny, będzie ona umożliwiać monitorowanie każdej strony witryny.

Wyrejestruj stronę wzorcową i edytuj ją za pomocą programu SharePoint Designer lub innego dowolnego edytora.

![](./media/sharepoint/03-master.png)

Dodaj kod przed tagiem </head>. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Lub na poszczególnych stronach
Aby monitorować ograniczony zestaw stron, dodaj skrypt oddzielnie do każdej strony. 

Wstaw składnik Web Part i osadź w nim fragment kodu.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Wyświetlanie danych aplikacji
Ponownie wdróż aplikację.

Wróć do bloku aplikacji w witrynie [Azure Portal](https://portal.azure.com).

Pierwsze zdarzenia będą wyświetlane w obszarze wyszukiwania. 

![](./media/sharepoint/09-search.png)

Jeśli oczekujesz większej ilości danych, kliknij przycisk Odśwież po kilku sekundach.

## <a name="capturing-user-id"></a>Przechwytywanie identyfikatora użytkownika
Fragment kodu standardowej strony sieci Web nie przechwytuje identyfikatora użytkownika z programu SharePoint, ale możesz to zrobić za pomocą niewielkiej modyfikacji.

1. Skopiuj klucz instrumentacji aplikacji z listy rozwijanej Podstawy w usłudze Application Insights. 

    ![](./media/sharepoint/02-props.png)

1. Zastąp klucz instrumentacji ciągiem „XXXX” we fragmencie kodu poniżej. 
2. Osadź skrypt w aplikacji programu SharePoint zamiast fragmentu kodu pobranego z portalu.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Następne kroki
* [Testy sieci Web](../../azure-monitor/app/monitor-web-app-availability.md) do monitorowania dostępności witryny.
* [Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) dla innych typów aplikacji.

<!--Link references-->


