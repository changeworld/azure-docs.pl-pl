---
title: Windows Phone Silverlight Engagement SDK Integration
description: Integrowanie usługi Azure Mobile Engagement z aplikacji Silverlight Windows Phone
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f041f19ceff366a34b1761bfdca12242f4bdf9b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Windows Phone Silverlight Engagement SDK Integration
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

W tej procedurze opisano Najprostszym sposobem, aby aktywować analizy usługi Azure Mobile Engagement i funkcji w aplikacji Windows Phone Silverlight monitorowania.

Poniższe kroki są wystarczająco aktywować raport dzienniki wymagane do obliczenia wszystkich statystyki dotyczące użytkowników, sesji, działania, awarii (Crash) i Technicals. Raport dzienniki wymagane do obliczenia innych danych statystycznych, takich jak zdarzenia i błędy zadań musi zostać wykonane ręcznie przy użyciu interfejsu API usługi Engagement (zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) poniżej) ponieważ te statystyki są zależne od aplikacji.

## <a name="supported-versions"></a>Obsługiwane wersje
Mobile Engagement SDK dla platformy Silverlight systemu Windows mogą być tylko zintegrowane aplikacji:

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Jeśli przeznaczona dla Windows Phone 8.1 (z systemem innym niż platformy Silverlight), zapoznaj się [uniwersalnych systemu Windows procedura integracji](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>Zainstaluj dodatek Silverlight SDK usługi Mobile Engagement
Mobile Engagement SDK dla platformy Silverlight systemu Windows jest dostępna jako pakietu Nuget, nazywany *MicrosoftAzure.MobileEngagement*. Można ją zainstalować z Visual Studio Menedżera pakietów Nuget. 

## <a name="add-the-capabilities"></a>Dodawanie funkcji
Engagement SDK wymaga prawidłowego funkcjonowania niektórych możliwości Windows Phone Silverlight SDK.

Otwórz z `WMAppManifest.xml` plików i pamiętaj, że następujące funkcje są zadeklarowane w `Capabilities` panelu:

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Inicjowanie usługi Engagement SDK
### <a name="engagement-configuration"></a>Konfiguracja usługi Engagement
Konfiguracja zaangażowania jest scentralizowana w `Resources\EngagementConfiguration.xml` pliku projektu.

Edytuj ten plik, aby określić:

* Parametry połączenia aplikacji między tagami `<connectionString>` i `<\connectionString>`.

Jeśli chcesz określić je w czasie wykonywania, należy wywołać metodę następujące przed zainicjowaniem agenta usługi Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

Ciąg połączenia dla aplikacji jest wyświetlana w portalu Azure.

### <a name="engagement-initialization"></a>Inicjowania usługi Engagement
Podczas tworzenia nowego projektu `App.xaml.cs` plik został wygenerowany. Ta klasa dziedziczy `Application` i zawiera wiele metod ważne. Będzie można również używany do inicjowania usługi Engagement SDK.

Modyfikowanie `App.xaml.cs`:

* Dodaj do Twojej `using` instrukcji:
  
      using Microsoft.Azure.Engagement;
* Wstaw `EngagementAgent.Instance.Init` w `Application_Launching` metody:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Wstaw `EngagementAgent.Instance.OnActivated` w `Application_Activated` metody:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> Firma Microsoft zdecydowanie zniechęcić można dodać inicjowania usługi Engagement w innym miejscu aplikacji. Jednak należy pamiętać, że `EngagementAgent.Instance.Init` metoda jest uruchamiana na dedykowanym wątku, a nie w wątku interfejsu użytkownika.
> 
> 

## <a name="basic-reporting"></a>Podstawowe raportowanie
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Zalecana metoda: przeciążenia sieci `PhoneApplicationPage` klas
Aby aktywować raportu wszystkie dzienniki wymagane przez zaangażowania można obliczyć użytkowników, sesji, działania, awarii (Crash) i statystyki technicznych, możesz po prostu wprowadzić wszystkie Twoje `PhoneApplicationPage` klasy podrzędne dziedziczą `EngagementPage` klasy.

Oto przykład tego, jak to zrobić w strony aplikacji. Możesz to zrobić to samo dla wszystkich stron w aplikacji.

#### <a name="c-source-file"></a>Plik źródłowy języka C#
Zmodyfikuj stronę `.xaml.cs` pliku:

* Dodaj do Twojej `using` instrukcji:
  
      using Microsoft.Azure.Engagement;
* Zastąp `PhoneApplicationPage` z `EngagementPage` :

**Bez usługi Engagement:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Z usługi Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Jeśli dziedziczy po stronie `OnNavigatedTo` metody, należy zachować ostrożność umożliwić `base.OnNavigatedTo(e)` wywołania. W przeciwnym razie nie będą raportowane działania. W rzeczywistości `EngagementPage` wywołuje `StartActivity` wewnątrz `OnNavigatedTo` metody.
> 
> 

#### <a name="xaml-file"></a>Plik XAML
Zmodyfikuj stronę `.xaml` pliku:

* Dodaj deklaracje przestrzeni nazw:
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Zastąp `phone:PhoneApplicationPage` z `engagement:EngagementPage` :

**Bez usługi Engagement:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Z usługi Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Zastąpienie zachowania domyślnego
Domyślnie nazwa klasy strony został zgłoszony jako nazwę działania, bez dodatkowych. Jeśli klasa korzysta z sufiksem "Page", Engagement spowoduje również usunięcie.

Jeśli chcesz zastąpić domyślne zachowanie dla nazwy, po prostu dodaj to w kodzie:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Jeśli chcesz zgłosić niektóre dodatkowe informacje o Twoich działaniach, możesz dodać to w kodzie:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Te metody są wywoływane z poziomu `OnNavigatedTo` metody na stronie.

### <a name="alternate-method-call-startactivity-manually"></a>Alternatywna metoda: wywołanie `StartActivity()` ręcznie
Jeśli nie możesz lub nie było przeciążyć Twojej `PhoneApplicationPage` klas, zamiast tego można uruchomić działań przez wywołanie metody `EngagementAgent` metody bezpośrednio.

Firma Microsoft zaleca wywołania `StartActivity` wewnątrz sieci `OnNavigatedTo` metody z PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Upewnij się, że prawidłowo zakończyć sesję.
> 
> Zestaw SDK automatycznie wywołuje `EndActivity` metody, gdy aplikacja zostanie zamknięta. W związku z tym jest **wysokiej** zaleca, aby wywołać `StartActivity` metody zmianie aktywności użytkownika i **nigdy** wywołać `EndActivity` — metoda. Ta metoda wysyła wiadomość do serwera usługi Engagement, czy bieżący użytkownik opuścił aplikacji i ma wpływ na wszystkie dzienniki aplikacji.
> 
> 

## <a name="advanced-reporting"></a>Zaawansowane raportowanie
Opcjonalnie, może zajść potrzeba raport aplikacji określonych zdarzeń, błędów i zadań, w tym celu należy użyć innych metod znalezione w `EngagementAgent` klasy. Interfejsu API usługi Engagement pozwala na korzystanie ze wszystkich zaawansowanych możliwości usługi Engagement.

Aby uzyskać więcej informacji, zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana
### <a name="disable-automatic-crash-reporting"></a>Wyłącz automatyczne zgłaszanie awarii
Możesz wyłączyć automatyczne awarii, funkcję zaangażowania raportowania. Następnie, gdy wystąpi nieobsługiwany wyjątek, Engagement nie będzie wykonywać żadnych czynności.

> [!WARNING]
> Jeśli chcesz wyłączyć tę funkcję, należy pamiętać, że po awarii nieobsługiwany nastąpi w aplikacji, Engagement nie będą wysyłały awarii **i** nie spowoduje zamknięcia sesji i zadania.
> 
> 

Aby wyłączyć automatyczne zgłaszanie awarii, wystarczy dostosować konfigurację, w zależności od sposobu należy zadeklarować jako:

#### <a name="from-engagementconfigurationxml-file"></a>Z `EngagementConfiguration.xml` pliku
Ustaw awarii raport `false` między `<reportCrash>` i `</reportCrash>` tagów.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Z `EngagementConfiguration` obiektu w czasie wykonywania
Ustaw awarii raportu przy użyciu obiektu EngagementConfiguration wartość false.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Tryb serii
Domyślnie raporty usługi Engagement rejestruje się w czasie rzeczywistym. Jeśli aplikacja zgłasza dzienniki bardzo często, lepiej jest buforu dzienniki i raportuj je w całości na regularne podstawy czasu (jest to nazywane "tryb serii").

Aby to zrobić, należy wywołać metodę:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argument jest wartością w **milisekund**. W dowolnym momencie Jeśli chcesz ponownie uaktywnić rejestrowania w czasie rzeczywistym, po prostu Wywołaj metodę bez parametrów lub z wartością 0.

Tryb serii nieco zwiększyć czas pracy baterii, ale ma wpływ na monitorowanie usługi Engagement: wszystkie czas trwania sesji i zadania zostanie zaokrąglony próg serii (w związku z tym sesji i zadania krótsze niż próg serii może nie być widoczna). Zaleca się przy użyciu progu w serii się niż 30000 (30s). Masz wiedzieć, który zapisano dzienniki są ograniczone do 300 elementów. W przypadku wysyłania jest zbyt długa może spowodować utratę niektórych dzienników.

> [!WARNING]
> Próg serii nie można skonfigurować do okresu mniejszym niż 1 sekunda. Jeśli spróbujesz to zrobić, zestaw SDK wyświetli śledzenia błąd i zostanie automatycznie zresetowana do wartości domyślnej, oznacza to zero sekund. Spowoduje to wyzwolenie zestaw SDK, aby zgłosić dzienniki w czasie rzeczywistym.
> 
> 

