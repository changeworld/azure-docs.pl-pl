---
title: Zaawansowane raporty dzięki zaangażowania MobileApps aplikacje uniwersalne systemu Windows
description: Integrowanie usługi Azure Mobile Engagement z uniwersalnych aplikacji systemu Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 88d28bbc32179698147ab7516cc414fd23ed4bd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Zaawansowane raporty dzięki zaangażowania uniwersalnych aplikacji systemu Windows SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Platforma uniwersalna systemu Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

W tym temacie opisano dodatkowe scenariusze raportowania w aplikacji uniwersalnych systemu Windows. Te scenariusze obejmują opcje, które można zastosować do aplikacji utworzonych w [wprowadzenie](mobile-engagement-windows-store-dotnet-get-started.md) samouczka.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Przed rozpoczęciem tego samouczka, należy najpierw wykonać [wprowadzenie](mobile-engagement-windows-store-dotnet-get-started.md) samouczka jest celowo bezpośredniego i prosty. Ten samouczek obejmuje dodatkowe opcje, które są dostępne.

## <a name="specifying-engagement-configuration-at-runtime"></a>Określenie konfiguracji zaangażowania w czasie wykonywania
Konfiguracja zaangażowania jest scentralizowana w `Resources\EngagementConfiguration.xml` pliku projektu jest, gdzie została określona w [wprowadzenie](mobile-engagement-windows-store-dotnet-get-started.md) tematu.

Można jednak również określić go w czasie wykonywania: należy wywołać przed zainicjowaniem agenta zaangażowania następującą metodę:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Zalecana metoda: przeciążenia sieci `Page` klas
Aktywować raportowania wszystkie dzienniki wymagane przez zaangażowania można obliczyć użytkowników, sesji, działania, awarii (Crash) i statystyki technicznych, aby wszystkie Twoje `Page` klasy podrzędne dziedziczą `EngagementPage` klasy.

Oto przykład strony aplikacji. Możesz to zrobić to samo dla wszystkich stron w aplikacji.

### <a name="c-source-file"></a>Plik źródłowy języka C#
Zmodyfikuj stronę `.xaml.cs` pliku:

* Dodaj do Twojej `using` instrukcji:
  
      using Microsoft.Azure.Engagement;
* Zastąp `Page` z `EngagementPage`:

**Bez usługi Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Z usługi Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Jeśli strona zastępuje metodę `OnNavigatedTo`, nie zapomnij wywołać metody `base.OnNavigatedTo(e)`. W przeciwnym razie działanie nie został zgłoszony ( `EngagementPage` wywołania `StartActivity` wewnątrz jego `OnNavigatedTo` metody).
> 
> 

### <a name="xaml-file"></a>Plik XAML
Zmodyfikuj stronę `.xaml` pliku:

* Dodaj deklaracje przestrzeni nazw:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Zastąp `Page` z `engagement:EngagementPage`:

**Bez usługi Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Z usługi Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Zastąpienie zachowania domyślnego
Domyślnie nazwa klasy strony został zgłoszony jako nazwę działania, bez dodatkowych. Jeśli klasa korzysta z sufiksem "Page", Engagement usuwa go.

Aby zastąpić domyślne zachowanie dla nazwy, Dodaj ten kod:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Aby zgłosić dodatkowe informacje o Twoich działaniach, Dodaj ten kod:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Te metody są wywoływane z poziomu `OnNavigatedTo` metody na stronie.

### <a name="alternate-method-call-startactivity-manually"></a>Alternatywna metoda: wywołanie `StartActivity()` ręcznie
Jeśli nie możesz lub nie było przeciążyć Twojej `Page` klas, zamiast tego można uruchomić działań przez wywołanie metody `EngagementAgent` metody bezpośrednio.

Firma Microsoft zaleca wywoływania `StartActivity` wewnątrz sieci `OnNavigatedTo` metody na stronie.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Upewnij się, że prawidłowo zakończyć sesję.
> 
> Uniwersalny zestaw SDK systemu Windows automatycznie wywołuje `EndActivity` metody, gdy aplikacja zostanie zamknięta. W związku z tym jest **wysokiej** zaleca, aby wywołać `StartActivity` metody zmianie aktywności użytkownika i **nigdy** wywołać `EndActivity` — metoda. Ta metoda powiadamia serwer zaangażowania, że bieżący użytkownik opuścił aplikacji, która będzie miało wpływ na wszystkie dzienniki aplikacji.
> 
> 

## <a name="advanced-reporting"></a>Zaawansowane raportowanie
Opcjonalnie, warto zgłaszać zdarzenia specyficzne dla aplikacji, błędy i zadań, aby to zrobić, użyj metody znaleziono w inne `EngagementAgent` klasy. Interfejs API usługi Engagement umożliwia korzystanie z zaawansowanych możliwości wszystkich zaangażowania.

Aby uzyskać więcej informacji, zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji uniwersalnych systemu Windows](mobile-engagement-windows-store-use-engagement-api.md).

