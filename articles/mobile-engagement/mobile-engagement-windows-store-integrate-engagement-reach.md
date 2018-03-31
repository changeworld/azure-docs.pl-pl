---
title: Integracja zestawu SDK osiągnąć uniwersalnych aplikacji systemu Windows
description: Integrowanie dotarcia do usługi Azure Mobile Engagement z uniwersalnych aplikacji systemu Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8472af2ac72a1976871970963851be775b19859b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Integracja zestawu SDK osiągnąć uniwersalnych aplikacji systemu Windows
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Wykonaj procedury integracji opisane w sekcji [Windows Universal integracji zestawu SDK usługi Engagement](mobile-engagement-windows-store-integrate-engagement.md) przed wykonaniem tego przewodnika.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Osadzanie SDK Reach usługi Engagement w projekcie uniwersalnych systemu Windows
Nie ma nic do dodania. `EngagementReach` referencje i zasoby są już w projekcie.

> [!TIP]
> Można dostosować obrazy znajdujące się w `Resources` folderu projektu, szczególnie ikona marki (domyślnie tej ikony Engagement). W aplikacjach uniwersalnych można również przenosić `Resources` folder dla projektu udostępnionego, aby udostępnić jego zawartość między aplikacjami, ale będą musieli zachować `Resources\EngagementConfiguration.xml` pliku w domyślnej lokalizacji, ponieważ jest on zależny platformy.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Włącz usługę powiadomień systemu Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x i Windows Phone 8.1 tylko
Aby można było używać **usługi powiadomień systemu Windows** (określanych jako WNS) w Twojej `Package.appxmanifest` plików na `Application UI` kliknij `All Image Assets` w polu bot po lewej stronie. Po prawej stronie pola w `Notifications`, zmień `toast capable` z `(not set)` do `(Yes)`.

### <a name="all-platforms"></a>Wszystkie platformy
Należy zsynchronizować aplikację do swojego konta Microsoft i platformy zaangażowania. Dla tego należy utworzyć konto lub zaloguj się na [Centrum deweloperów systemu windows](https://dev.windows.com). Po wykonaniu tej Tworzenie nowej aplikacji i znajdowanie identyfikatora SID i klucz tajny. Na frontonie zaangażowania przejdź na ustawienia aplikacji w `native push` i Wklej swoje poświadczenia. Następnie kliknij prawym przyciskiem myszy projekt, wybierz opcję `store` i `Associate App with the Store...`. Wystarczy wybrać aplikację przed ma utworzyć, aby ją zsynchronizować.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicjowanie Reach usługi Engagement SDK
Modyfikowanie `App.xaml.cs`:

* Wstaw `EngagementReach.Instance.Init` tuż po `EngagementAgent.Instance.Init` w Twojej `InitEngagement` metody:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` Działa w dedykowanym wątku. Nie masz zrobić samodzielnie.

> [!NOTE]
> Jeśli używasz powiadomień wypychanych w innym miejscu w aplikacji, trzeba [udostępnianie kanału wypychania](#push-channel-sharing) z Reach usługi Engagement.
> 
> 

## <a name="integration"></a>Integracja
Engagement udostępnia dwa sposoby dodawania międzysegmentowe widoków w przypadku anonsów i sond i Reach transparentach w aplikacji w aplikacji: integracja z nakładką i integracja ręczne widoki sieci web. Nie należy łączyć oba podejścia na tej samej stronie.

Wybór między dwoma integracji można podsumować w ten sposób:

* Integracja z nakładką może wybrać, jeśli strony już dziedziczy od agenta `EngagementPage`, jest wystarczy zastępowanie `EngagementPage` przez `EngagementPageOverlay` i `xmlns:engagement="using:Microsoft.Azure.Engagement"` przez `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` na swoich stronach.
* Możesz wybrać widoki sieci web integracji ręczne Aby precyzyjnie umieścić osiągnąć interfejsu użytkownika w ramach strony lub jeśli nie chcesz dodać kolejny poziom dziedziczenia do stron. 

### <a name="overlay-integration"></a>Integracja z nakładką
Nakładka zaangażowania dynamicznie dodaje elementy interfejsu użytkownika używana do wyświetlania kampanie Zasięgowe na stronie. Jeśli nakładki nie własnych układu należy rozważyć widoki sieci web integracji ręczne zamiast tego.

W przypadku zmiany pliku .xaml `EngagementPage` odwołanie do `EngagementPageOverlay`

* Dodaj deklaracje przestrzeni nazw:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Zastąp `engagement:EngagementPage` z `engagement:EngagementPageOverlay`:

**Z EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**With EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Następnie w pliku .cs tag strony w `EngagementPageOverlay` zamiast `EngagementPage` i zaimportować `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Zastąp `EngagementPage` z `EngagementPageOverlay`:

**Z EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**With EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Dodaje nakładki Engagement `Grid` element na stronie składa się z układu i dwa `WebView` elementy jeden na banerze i drugi dla widoku międzysegmentowe.

Można dostosowywać elementy nakładki bezpośrednio w `EngagementPageOverlay.cs` pliku.

### <a name="web-views-manual-integration"></a>Integracja ręczna widoki sieci Web
Reach będzie wyszukiwanie stron dla dwóch `WebView` elementy odpowiada za wyświetlanie banera i międzysegmentowe widoku. Jedyną operacją, musisz wykonać jest dodanie tych dwóch `WebView` elementy gdzieś na swoich stronach Oto przykład:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


W tym przykładzie `WebView` elementy są rozciągany w celu dopasowania ich kontenera, który automatycznie ponownie rozmiary je w przypadku zmiany rozmiaru ekranu obrotu lub okno.

> [!WARNING]
> Ważne jest, aby zachować tego samego nazewnictwa `engagement_notification_content` i `engagement_announcement_content` dla `WebView` elementów. Zasięg identyfikuje je według nazwy. 
> 
> 

## <a name="handle-datapush-optional"></a>Dojście datapush (opcjonalnie)
Możliwość odbierania Reach wypychania danych aplikacji, należy zaimplementować dwa zdarzenia klasy EngagementReach:

W pliku App.xaml.cs w Konstruktorze App() należy dodać:

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

Następnie ustaw zawartość `EngagementReach.Instance.Handler` pole z niestandardowego obiektu w Twojej `App.xaml.cs` klas w ramach `App()` — metoda.

**Przykładowy kod:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Domyślnie program zaangażowania używa własną implementację `EngagementReachHandler`.
> Nie trzeba tworzyć własne, a jeśli tak zrobisz, nie trzeba co metoda musi zostać zastąpiona. Domyślnym zachowaniem jest wybierz obiekt podstawowy zaangażowania.
> 
> 

### <a name="web-view"></a>Widok sieci Web
Domyślnie Reach zostaną użyte zasoby osadzone biblioteki dll do wyświetlania powiadomień i stron.

Aby zapewnić pełne dostosowywanie możliwości używamy są tylko widoku sieci web. Jeśli chcesz dostosować układów bezpośrednio zastąpienie plików zasobów `EngagementAnnouncement.html` i `EngagementNotification.html`. Engagement wymaga cały kod w `<body></body>` by działała poprawnie. Możesz dodać tag zewnętrzne, ale `engagement_webview_area`.

Można korzystać z własnych zasobów.

Można zastąpić `EngagementReachHandler` metod w Twojej podklasy zaangażowania używać układów, ale należy sprawdzić osadzonych mechanizm engagement:

**Przykładowy kod:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Domyślnie jest AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Reprezentuje plik html, który projektowania zawartości wiadomości wypychanych (tekst anonsu, anoucement sieci Web i anonsu sondowania). Jest AnnouncementName `engagement_announcement_content`. Jest to nazwa projektu widoku sieci Web ze strony xaml.

Jest NotfificationHTML `ms-appx-web:///Resources/EngagementNotification.html`. Reprezentuje plik html, który zaprojektować powiadomienie o wiadomości wypychanych. Jest NotfificationName `engagement_notification_content`. Jest to nazwa projektu widoku sieci Web ze strony xaml.

### <a name="customization"></a>Dostosowywanie
Możesz dostosować powiadomień i anonsu widoku sieci web ma użytkownik w przypadku zachowania obiektu zaangażowania. Zwróć uwagę na to, że webview obiektu opisano trzy razy — po raz pierwszy w Twojej xaml, w pliku .cs w metodzie "setwebview()" po raz drugi i trzeci czasu w pliku html.

* W Twojej xaml opisywane bieżącego składnika układu graficznego w widoku sieci Web.
* W pliku .cs można zdefiniować "setwebview()", w którym można ustawić wymiar dwóch widoku sieci Web (powiadomienia, anons). Jest bardzo skuteczne, gdy aplikacja jest zmiana rozmiaru.
* W pliku html zaangażowania przedstawione zawartość widoku sieci Web, projektowanie i pozycji elementów między sobą.

### <a name="launch-message"></a>Uruchamianie wiadomości
Gdy użytkownik kliknie powiadomienie systemowe (alert), Engagement uruchamia aplikację, Załaduj zawartość wiadomości wypychanych i wyświetlenia strony do odpowiedniego kampanii.

Występuje opóźnienie między uruchamiania aplikacji i wyświetlania strony (w zależności od szybkości sieci).

Aby wskazać użytkownikowi coś ładowania, należy podać visual informacji, takich jak pasek postępu lub wskaźnik postępu. Zaangażowania nie może obsłużyć, ale udostępnia kilka programy obsługi dla Ciebie.

Aby zaimplementować metodę wywołania zwrotnego, w pliku App.xaml.cs "{} App() publiczny" Dodaj:

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

Wywołania zwrotnego można ustawić w metodzie "Publicznego App() {}" z `App.xaml.cs` plików, najlepiej przed `EngagementReach.Instance.Init()` wywołania.

> [!TIP]
> Każdy program obsługi jest wywoływana przez wątek interfejsu użytkownika. Masz nie martw się, używając MessageBox lub coś związanych z interfejsu użytkownika.
> 
> 

## <a id="push-channel-sharing"></a> Wypychanie do udostępniania kanału
Jeśli używane są powiadomienia wypychane do innych celów w aplikacji następnie należy użyć kanału wypychanych udostępniania funkcji zestawu SDK usługi Engagement. Pozwoli to uniknąć brakujących wypychania.

* Możesz podać własne kanału wypychania do inicjowania Reach usługi Engagement. Zestaw SDK będą używać go zamiast nowego żądania.

Zaktualizuj inicjowania Reach usługi Engagement za pomocą wypychania kanału w `InitEngagement` metody z `App.xaml.cs` pliku:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Alternatywnie Jeśli chcesz, aby korzystać z kanału wypychanych po możesz inicjowania Reach można ustawiać wywołanie zwrotne Reach usługi Engagement uzyskać kanału wypychanych raz jest tworzona przez zestaw SDK.

Ustaw wywołanie zwrotne w dowolnym miejscu **po** inicjowania zasięgu:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Porada schematu niestandardowego
Udostępniamy Użyj schematu niestandardowego. Możesz wysłać innego typu identyfikatora URI z frontonu zaangażowania do użycia w aplikacji usługi engagement. Domyślny schemat, takich jak `http, ftp, ...` są zarządzanie przez system Windows, zostanie okno wierszu są zainstalowane na urządzeniu domyślnej aplikacji. Można również utworzyć własny niestandardowy schemat dla aplikacji.

Prosty sposób, aby ustawić schematu niestandardowego w aplikacji jest otwarcie Twojej `Package.appxmanifest` Przejdź w `Declarations` panelu. Wybierz `Protocol` w deklaracjach dostępne przewiń okno i dodaj go. Edytuj `Name` pola z Twojej nowy protokół żądaną nazwę.

Aby użyć tego protokołu, edytować Twojej `App.xaml.cs` z `OnActivated` metody i nie zapomnij inicjowania usługi engagement tutaj również:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

