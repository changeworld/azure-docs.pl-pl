---
title: Windows Phone Silverlight SDK procedur uaktualniania
description: Windows Phone Silverlight SDK procedur uaktualniania dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 489b005c37ddb842a2501e89c07fb34b091346e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK procedur uaktualniania
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Jeśli już jest zintegrowany starszej wersji naszego zestawu SDK do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Należy wykonać kilka procedur, jeśli pominięte kilka wersji zestawu SDK. Na przykład w przypadku migrowania z 0.10.1 do 0.11.0, należy najpierw wykonać procedurę "od 0.9.0 do 0.10.1" następnie procedury "od 0.10.1 do 0.11.0".

## <a name="from-200-to-330"></a>Z 2.0.0 do 3.3.0
### <a name="test-logs"></a>Dzienniki testów
Dzienniki konsoli utworzonego przez zestaw SDK można teraz włączone/wyłączone/odfiltrowane. Aby dostosować to, zaktualizuj właściwość `EngagementAgent.Instance.TestLogEnabled` do jednej z dostępnych wartości `EngagementTestLogLevel` wyliczenia, na przykład:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>Z 1.1.1 do 2.0.0
Poniżej opisano sposób migracji integracji zestawu SDK usługi Capptain oferowane przez Capptain SAS w aplikacji obsługiwane przez usługę Azure Mobile Engagement. 

> [!IMPORTANT]
> Capptain i usługi Mobile Engagement nie są takie same usługi i procedury przedstawionej poniżej tylko prezentuje sposób migracji aplikacji klienckiej. Migrowanie zestawu SDK w aplikacji wiadomości nie będzie migrację danych z serwerów Capptain do serwerów usługi Mobile Engagement
> 
> 

W przypadku migracji z wcześniejszej wersji, przejrzyj witrynę sieci web Capptain do pierwszej kolejności przeprowadzenie migracji 1.1.1, zastosuj następującą procedurę

### <a name="nuget-package"></a>Pakiet Nuget
Zastąp **Capptain.WindowsPhone** przez **MicrosoftAzure.MobileEngagement** pakietu Nuget.

### <a name="applying-mobile-engagement"></a>Applying Mobile Engagement
Zestaw SDK używany jest termin `Engagement`. Musisz zaktualizować projektu do dopasowania tej zmiany.

Konieczne jest odinstalowanie bieżącego Capptain pakietu nuget. Należy wziąć pod uwagę, że wszystkie zmiany w folderze Capptain zasoby zostaną usunięte. Jeśli chcesz zachować te pliki, a następnie utworzyć ich kopię.

Po tym należy zainstalować pakiet nuget usługi Microsoft Azure Engagement w projekcie. Można go znaleźć bezpośrednio na [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Ta akcja zastępuje wszystkie pliki zasoby używane przez usługi Engagement i dodaje nowe DLL zaangażowania do odwołania projektu.

Należy wyczyścić przez usunięcie odwołania do biblioteki DLL Capptain odwołania projektu. Jeśli nie wprowadzisz to, wersja Capptain spowoduje konflikt i nastąpi błędy.

Jeśli dostosowano Capptain zasobów, skopiuj stare pliki zawartości i wklej je w nowych plików zaangażowania. Należy pamiętać, że można zaktualizować pliku xaml, jak i cs.

Po wykonaniu tych kroków należy tylko zastąpić starego odwołania Capptain przez nowego odwołania zaangażowania.

1. Wszystkie przestrzenie nazw Capptain wymagają aktualizacji.
   
    Przed migracją:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Po zakończeniu migracji:
   
        using Microsoft.Azure.Engagement;
2. Wszystkie klasy Capptain, które zawierają "Capptain" powinien zawierać "Zaangażowania".
   
    Przed migracją:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Po zakończeniu migracji:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Pliki xaml Capptain przestrzeni nazw i atrybuty także zmienić.
   
    Przed migracją:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Po zakończeniu migracji:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Dla innych zasobów, takich jak obrazy Capptain należy pamiętać, że one również zmieniono za pomocą "Engagement".

### <a name="application-id--sdk-key"></a>Identyfikator aplikacji / klucz zestawu SDK
Zaangażowania używa parametrów połączenia. Nie trzeba określić identyfikator i klucz zestawu SDK usługi Mobile Engagement, wystarczy tylko określić parametry połączenia. Możesz można skonfigurować go na plik EngagementConfiguration.

Konfiguracja usługi Engagement może zostać ustawiona Twojej `Resources\EngagementConfiguration.xml` pliku projektu.

Edytuj ten plik, aby określić:

* Parametry połączenia aplikacji między tagami `<connectionString>` i `<\connectionString>`.

Jeśli chcesz określić je w czasie wykonywania, należy wywołać metodę następujące przed zainicjowaniem agenta usługi Engagement:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

Ciąg połączenia dla aplikacji jest wyświetlana w portalu Azure.

### <a name="items-name-change"></a>Zmiana nazwy elementów
Wszystkie elementy o nazwie *capptain* została nazwana *engagement*. Podobnie dla *Capptain* do *Engagement*.

Przykłady często używanych elementów Capptain:

* CapptainConfiguration teraz nazwę EngagementConfiguration
* Teraz o nazwie EngagementAgent CapptainAgent
* Teraz o nazwie EngagementReach CapptainReach
* Teraz o nazwie EngagementHttpConfig CapptainHttpConfig
* Teraz o nazwie GetEngagementPageName GetCapptainPageName

Należy pamiętać, że zmiany nazwy wpływa na przesłoniętej metody.

