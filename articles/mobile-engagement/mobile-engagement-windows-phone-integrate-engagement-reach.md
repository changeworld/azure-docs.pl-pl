---
title: Windows Phone Silverlight dotarcia do integracji zestawu SDK
description: Integrowanie dotarcia do usługi Azure Mobile Engagement z aplikacji Silverlight Windows Phone
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 34e5ce414ebf72fbecef6c921e57128e2658c921
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight dotarcia do integracji zestawu SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Wykonaj procedury integracji opisane w sekcji [integracji zestawu SDK usługi Engagement Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) przed wykonaniem tego przewodnika.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Osadzanie SDK Reach usługi Engagement w projekcie systemu Windows Phone Silverlight
Nie ma nic do dodania. `EngagementReach` referencje i zasoby są już w projekcie.

> [!TIP]
> Można dostosować obrazy znajdujące się w `Resources` folderu projektu, szczególnie ikona marki (domyślnie tej ikony Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Dodawanie funkcji
Zestaw SDK Reach usługi Engagement musi niektóre dodatkowe funkcje.

Otwórz z `WMAppManifest.xml` plików i pamiętaj, że następujące funkcje są deklarowane jako:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

Pierwsza z nich jest używany przez usługi MPNS umożliwia wyświetlanie wyskakujących powiadomień. Drugi służy do osadzania zadania przeglądarki w zestawie SDK.

Edytuj `WMAppManifest.xml` plik i dodać wewnątrz `<Capabilities />` tagu:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Włączanie usługi powiadomień wypychanych firmy Microsoft
Aby można było używać **Microsoft Push Notification Service** (określanych jako usługi MPNS) z `WMAppManifest.xml` plik musi mieć `<App />` tagu z `Publisher` atrybutów należy ustawić nazwę projektu.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicjowanie Reach usługi Engagement SDK
### <a name="engagement-configuration"></a>Konfiguracja usługi Engagement
Konfiguracja zaangażowania jest scentralizowana w `Resources\EngagementConfiguration.xml` pliku projektu.

Edytuj ten plik, aby określić konfigurację zasięgu:

* *Opcjonalne*, wskazuje, czy włączono natywnych powiadomień wypychanych (MPNS), czy między `<enableNativePush>` i `</enableNativePush>` tagi (`true` domyślnie).
* *Opcjonalne*, wskazuje nazwę kanału wypychanych między `<channelName>` i `</channelName>` tagów, podaj takie same aplikacji mogą obecnie korzystać lub pozostaw puste.

Jeśli chcesz określić je w czasie wykonywania, należy wywołać metodę następujące przed zainicjowaniem agenta usługi Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Można określić nazwę kanału wypychanych usługi MPNS aplikacji. Domyślnie program zaangażowania tworzy nazwę, w oparciu o identyfikator appId. Użytkownik nie ma potrzeby należy wpisać nazwę użytkownika, z wyjątkiem przypadków, jeśli planujesz używać kanału wypychanych poza zaangażowania.
> 
> 

### <a name="engagement-initialization"></a>Inicjowania usługi Engagement
Modyfikowanie `App.xaml.cs`:

* Dodaj do Twojej `using` instrukcji:
  
      using Microsoft.Azure.Engagement;
* Wstaw `EngagementReach.Instance.Init` tuż po `EngagementAgent.Instance.Init` w `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Wstaw `EngagementReach.Instance.OnActivated` w `Application_Activated` metody:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init` Działa w dedykowanym wątku. Nie masz zrobić samodzielnie.
> 
> 

## <a name="app-store-submission-considerations"></a>Zagadnienia dotyczące przesyłania magazynu aplikacji
Microsoft nakłada niektórych reguł przy użyciu powiadomień wypychanych:

Z Microsoft [zasady aplikacji] dokumentacji, sekcja 2.9:

1) Wymagać od użytkownika akceptacji do odbierania powiadomień wypychanych. Następnie w ustawieniach, należy dodać sposób, aby wyłączyć powiadomienia wypychane.

Obiekt EngagementReach udostępnia dwie metody do zarządzania — w/zgody, `EnableNativePush()` i `DisableNativePush()`. Można na przykład utworzyć opcję w ustawieniach z przełącznikiem, aby wyłączyć lub włączyć usługi MPNS.

Można też dezaktywowania usługi MPNS za pomocą konfiguracji usługi Engagement\<konfiguracji systemu windows-phone-sdk-reach —\>.

> 2.9.1) aplikacji należy najpierw opisano powiadomienia, które mają być świadczone i **uzyskać wyraźnej zgody użytkownika (opt — ruch przychodzący)**, i **podać mechanizm, za pomocą którego użytkownik może zrezygnować z otrzymywania powiadomień wypychanych**. Wszystkie powiadomienia realizowane przy użyciu usługi powiadomień wypychanych firmy Microsoft musi być zgodna z dostarczony dla użytkownika opis i musi spełniać wszystkie odpowiednie [zasady aplikacji] [ Content Policies] i [dodatkowe wymagania dotyczące określonych typów aplikacji].
> 
> 

2) Nie należy używać zbyt wiele powiadomień wypychanych. Zaangażowania obsługi powiadomień dla Ciebie.

> 2.9.2) aplikacji i jej korzystania z usługi powiadomień wypychanych firmy Microsoft musi nie nadmiernie pojemność sieci lub przepustowości usługi powiadomień wypychanych firmy Microsoft, albo w przeciwnym razie nadmiernie obciążać Windows Phone lub innych urządzeń firmy Microsoft lub usługi z powiadomień wypychanych nadmiernego, zgodnie z ustaleniami firmy Microsoft uznania i musi nie uszkodzić lub zakłócać żadnych sieci firmy Microsoft lub serwerów lub serwerów innych firm ani sieciach podłączonych do usługi powiadomień wypychanych firmy Microsoft.
> 
> 

3) Nie należy polegać na usługi MPNS wysyłanie informacji criticals. Zaangażowania używana jest usługa MPNS, więc ta reguła ma zastosowanie również kampanii tworzone wewnątrz stopnia zaangażowania frontonu.

> 2.9.3) usługi powiadomień wypychanych firmy Microsoft nie może być używany do wysyłania powiadomień, które wymagają krytycznych lub w inny sposób mogą mieć wpływ na ważne życia lub śmierci, w tym bez ograniczenia krytyczne powiadomień dotyczących medyczne urządzenia lub warunku. MICROSOFT WYRAŹNIE NIE UDZIELA ŻADNYCH GWARANCJI CZY KORZYSTANIA Z USŁUGI POWIADOMIEŃ WYPYCHANYCH FIRMY MICROSOFT ORAZ DOSTARCZANIA POWIADOMIEŃ USŁUGI POWIADOMIEŃ WYPYCHANYCH FIRMY MICROSOFT ZOSTANIE PRZERWANA, BŁĄD SWOBODNEGO LUB W INNY SPOSÓB GWARANCJI SĄ DOKONYWANE NA ZASADZIE W CZASIE RZECZYWISTYM.
> 
> 

**Nie możemy zagwarantować aplikacji przekazuje proces weryfikacji, jeśli nie przestrzega te zalecenia.**

## <a name="handle-data-push-optional"></a>Obsługa wypychanie danych (opcjonalnie)
Możliwość odbierania Reach wypychania danych aplikacji, należy zaimplementować dwa zdarzenia klasy EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };

    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Widać, że wywołanie zwrotne każdej metody zwraca wartość logiczną. Zaangażowania wysyła opinię do jego zaplecza po wysłaniu wypychanie danych. Jeśli wywołanie zwrotne zwraca wartość FAŁSZ, `exit` opinii będzie wysyłania. W przeciwnym razie będzie `action`. Jeśli ustawiono bez wywołania zwrotnego dla zdarzeń, `drop` Opinia zostanie zwrócony do zaangażowania.

> [!WARNING]
> Zaangażowania nie będzie mógł odbierać opinie wielokrotności do wypychania danych. Jeśli planujesz ustawić kilka programów obsługi na zdarzenie, należy pamiętać, że opinii będzie odpowiadać ostatniej jedną wysyłane. W takim przypadku zaleca się zawsze zwraca tę samą wartość, aby uniknąć mylące opinii na frontonu.
> 
> 

## <a name="customize-ui-optional"></a>Dostosowywanie interfejsu użytkownika (opcjonalne)
### <a name="first-step"></a>Pierwszym krokiem
Firma Microsoft umożliwiają dostosowanie reach interfejsu użytkownika.

Aby to zrobić, należy utworzyć podklasy `EngagementReachHandler` klasy.

**Przykładowy kod:**

    using Microsoft.Azure.Engagement;

    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Następnie ustaw zawartość `EngagementReach.Instance.Handler` pole z niestandardowego obiektu w Twojej `App.xaml.cs` klas w ramach `Application_Launching` — metoda.

**Przykładowy kod:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Domyślnie program zaangażowania używa własną implementację `EngagementReachHandler`. Nie trzeba tworzyć własne, a jeśli tak zrobisz, nie trzeba co metoda musi zostać zastąpiona. Domyślnym zachowaniem jest wybierz obiekt podstawowy zaangażowania.
> 
> 

### <a name="layouts"></a>Układy
Domyślnie Reach zostaną użyte zasoby osadzone biblioteki dll do wyświetlania powiadomień i stron.

Można jednak podjąć decyzję o Użyj własnych zasobów, aby odzwierciedlić oznakowanie w tych składników.

Można zastąpić `EngagementReachHandler` metod w Twojej podklasy mówić zaangażowania używania układów:

**Przykładowy kod:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> `CreateNotification` Metoda może zwracać wartości zerowej. Nie będą wyświetlane powiadomienia, które zostaną usunięte kampanii reach.
> 
> 

Aby uprościć implementacji układu, firma Microsoft udostępnia również własnej xaml, który może służyć jako podstawa dla kodu. Znajdują się one w archiwum Engagement SDK (/ src/reach /).

> [!WARNING]
> Źródeł, które firma Microsoft udostępnia są dokładnie takie same jak których używamy. Dlatego jeśli chcesz zmodyfikować je bezpośrednio, nie zapomnij zmienić przestrzeni nazw i nazwę.
> 
> 

### <a name="notification-position"></a>Pozycja powiadomień
Domyślnie na dole po lewej stronie aplikacji zostanie wyświetlone powiadomienie w aplikacji. Możesz zmienić to zachowanie przez zastąpienie `GetNotificationPosition` metody `EngagementReachHandler` obiektu.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Obecnie można wybrać `BOTTOM` (ustawienie domyślne) i `TOP` pozycji.

### <a name="launch-message"></a>Uruchamianie wiadomości
Gdy użytkownik kliknie powiadomienie systemowe (alert), Engagement uruchamia aplikację, Załaduj zawartość wiadomości wypychanych i wyświetlenia strony do odpowiedniego kampanii.

Występuje opóźnienie między uruchamiania aplikacji i wyświetlania strony (w zależności od szybkości sieci).

Aby wskazać użytkownikowi coś ładowania, należy podać visual informacji, takich jak pasek postępu lub wskaźnik postępu. Zaangażowania nie może obsłużyć, ale udostępnia kilka programy obsługi dla Ciebie.

Aby zaimplementować metodę wywołania zwrotnego, należy wykonać:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Wywołania zwrotnego można ustawić w Twojej `Application_Launching` metody z `App.xaml.cs` plików, najlepiej przed `EngagementReach.Instance.Init()` wywołania.

> [!TIP]
> Każdy program obsługi jest wywoływana przez wątek interfejsu użytkownika. Masz nie martw się, używając MessageBox lub coś związanych z interfejsu użytkownika.
> 
> 

[zasady aplikacji]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[dodatkowe wymagania dotyczące określonych typów aplikacji]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

