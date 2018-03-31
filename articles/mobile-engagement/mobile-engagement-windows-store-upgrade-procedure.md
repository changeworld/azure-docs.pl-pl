---
title: Procedury uaktualniania uniwersalnych aplikacji systemu Windows SDK
description: Procedury uaktualniania systemu Windows Universal SDK aplikacji dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a9d6cbcdf353f7eea991c344c3efe65378abe336
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedury uaktualniania uniwersalnych aplikacji systemu Windows SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Jeśli już jest zintegrowany starszą wersję programu zaangażowania do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Należy wykonać kilka procedur, jeśli pominięte kilka wersji zestawu SDK. Na przykład w przypadku migrowania z 0.10.1 do 0.11.0, należy najpierw wykonać procedurę "od 0.9.0 do 0.10.1" następnie procedury "od 0.10.1 do 0.11.0".

## <a name="from-330-to-340"></a>Z 3.3.0 do 3.4.0
### <a name="test-logs"></a>Dzienniki testów
Dzienniki konsoli utworzonego przez zestaw SDK można teraz włączone/wyłączone/odfiltrowane. Aby dostosować to, zaktualizuj właściwość `EngagementAgent.Instance.TestLogEnabled` do jednej z dostępnych wartości `EngagementTestLogLevel` wyliczenia, na przykład:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Zasoby
Udoskonalono nakładki Reach. Jest ona częścią zasoby pakietu NuGet zestawu SDK.

Podczas uaktualniania do nowej wersji zestawu SDK można wybrać, czy chcesz zachować istniejące pliki z folderu nakładki zasobów lub nie:

* Jeśli poprzednie nakładki działa dla Ciebie lub jest integrowany `WebView` elementy ręcznie następnie chcesz zachować istniejących plików, to nadal będzie działać. 
* Jeśli chcesz zaktualizować nowe nakładce, po prostu zastąpić całego `overlay` folder z Twoich zasobów nową z pakietu SDK (aplikacji platformy uniwersalnej systemu Windows: po uaktualnieniu, możesz pobrać ze strony % USERPROFILE % nowy folder nakładki\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Przy użyciu nowego nakładki zastąpią wszelkie dostosowania z poprzedniej wersji.
> 
> 

## <a name="from-320-to-330"></a>Z 3.2.0 do 3.3.0
### <a name="resources"></a>Zasoby
Ten krok dotyczy tylko zasobów niestandardowych. Jeśli dostosowano zasobów udostępnianych przez zestaw SDK (html, obrazy, nakładki) następnie trzeba kopii zapasowej je przed uaktualnieniem i ponownie zastosuj dostosowanie uaktualnionego zasobów.

## <a name="from-310-to-320"></a>Z 3.1.0 do 3.2.0
### <a name="resources"></a>Zasoby
Ten krok dotyczy tylko zasobów niestandardowych. Jeśli dostosowano zasobów udostępnianych przez zestaw SDK (html, obrazy, nakładki) następnie trzeba kopii zapasowej je przed uaktualnieniem i ponownie zastosuj dostosowanie uaktualnionego zasobów.

### <a name="webview-integration"></a>Integracja z widoku sieci Web
Niektóre ulepszenia zgodne urządzenie różnych rozmiarach zostały wprowadzone w tej wersji. Upewnij się, że integracją z widoku sieci Web zgodne z następujących czynności:

W Twojej XAML strony ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

W pliku skojarzone CS:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>Z 2.0.0 do 3.0.0
### <a name="resources"></a>Zasoby
Ten krok dotyczy tylko zasobów niestandardowych. Jeśli dostosowano zasobów udostępnianych przez zestaw SDK (html, obrazy, nakładki) następnie trzeba kopii zapasowej je przed uaktualnieniem i ponownie zastosuj dostosowanie uaktualnionego zasobów.

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

Po tym należy zainstalować pakiet nuget usługi Microsoft Azure Engagement w projekcie. Znajdziesz ją bezpośrednio witrynie sieci Web [nuget]. lub indeks tutaj. Ta akcja zastępuje wszystkie pliki zasoby używane przez usługi Engagement i dodaje nowe DLL zaangażowania do odwołania projektu.

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
4. Nakładki strony zmiany
   
   > [!IMPORTANT]
   > Zmienia także nakładki. Jego nowa przestrzeń nazw to `Microsoft.Azure.Engagement.Overlay`. Składa się z stosowaną w pliku xaml, jak i cs. Ponadto `CapptainGrid` ma być nazwane `EngagementGrid`, `capptain_notification_content` i `capptain_announcement_content` są nazywane `engagement_notification_content` i `engagement_announcement_content`.
   > 
   > 
   
    Do nakładki:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Staje się:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Dla innych zasobów takich jak obrazy Capptain i pliki HTML należy pamiętać, że one również zmieniono za pomocą "Engagement".

### <a name="project-declaration"></a>Deklaracja projektu
Na Package.appxmanifest `File Type Associations` został zaktualizowany od:

* capptain\_osiągnąć\_zawartości do usługi engagement\_osiągnąć\_zawartości
* capptain\_dziennika\_pliku do usługi engagement\_dziennika\_pliku

### <a name="application-id--sdk-key"></a>Identyfikator aplikacji / klucz zestawu SDK
Zaangażowania używa parametrów połączenia. Nie trzeba określić identyfikator i klucz zestawu SDK usługi Mobile Engagement, wystarczy tylko określić parametry połączenia. Możesz można skonfigurować go na plik EngagementConfiguration.

Konfiguracja usługi Engagement może zostać ustawiona Twojej `Resources\EngagementConfiguration.xml` pliku projektu.

Edytuj ten plik, aby określić:

* Parametry połączenia aplikacji między tagami `<connectionString>` i `<\connectionString>`.

Jeśli chcesz określić je w czasie wykonywania, należy wywołać metodę następujące przed zainicjowaniem agenta usługi Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

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

