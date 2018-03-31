---
title: Integracja zestawu SDK zaangażowania uniwersalnych aplikacji systemu Windows
description: Integrowanie usługi Azure Mobile Engagement z uniwersalnych aplikacji systemu Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: dc075ffe6565b4ef60e77f74ba4f065629c99952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integracja zestawu SDK zaangażowania uniwersalnych aplikacji systemu Windows
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Platforma uniwersalna systemu Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

W tej procedurze opisano Najprostszym sposobem, aby aktywować usługi Engagement analizy i monitorowania aplikacji uniwersalnych systemu Windows.

Poniższe kroki są wystarczająco aktywować raport dzienniki wymagane do obliczenia wszystkich statystyki dotyczące użytkowników, sesji, działania, awarii (Crash) i Technicals. Raport dzienniki wymagane do obliczenia innych danych statystycznych, takich jak zdarzenia i błędy zadań musi zostać wykonane ręcznie przy użyciu interfejsu API usługi Engagement (zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji uniwersalnych systemu Windows](mobile-engagement-windows-store-use-engagement-api.md) od tych statystyki są aplikacji zależnej.

## <a name="supported-versions"></a>Obsługiwane wersje
Tylko Mobile Engagement SDK dla uniwersalnych aplikacji systemu Windows mogą być zintegrowane środowisko wykonawcze systemu Windows i aplikacji platformy uniwersalnej systemu Windows:

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (wersje desktop i mobile rodzin)

> [!NOTE]
> Jeśli przeznaczona dla systemu Windows Phone Silverlight, zapoznaj się [procedura integracji systemu Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Zainstaluj zestaw SDK usługi Mobile Engagement uniwersalnych aplikacji
### <a name="all-platforms"></a>Wszystkie platformy
Mobile Engagement SDK dla aplikacji uniwersalnej systemu Windows jest dostępna jako pakietu Nuget, nazywany *MicrosoftAzure.MobileEngagement*. Można ją zainstalować z Visual Studio Menedżera pakietów Nuget.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x i Windows Phone 8.1
NuGet automatycznie wdraża zasobów zestawu SDK w `Resources` folder w katalogu głównym projektu aplikacji.

### <a name="windows-10-universal-windows-platform-applications"></a>Aplikacji platformy uniwersalnej systemu Windows 10 systemu Windows
NuGet nie automatycznie wdrażać zasobów zestawu SDK w jeszcze aplikacji platformy uniwersalnej systemu Windows. Należy to zrobić ręcznie do momentu powraca wdrażania zasobów w NuGet:

1. Otwieranie Eksploratora plików w sieci.
2. Przejdź do następującej lokalizacji (**x.x.x** jest wersją w przypadku instalowania usługi Engagement): *% USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\  **x.x.x**\\content\win81*
3. Przeciągnij i upuść **zasobów** folder w Eksploratorze plików w katalogu głównym projektu programu Visual Studio.
4. W programie Visual Studio wybierz projektu i Aktywuj **Pokaż wszystkie pliki** ikonę nad **Eksploratora rozwiązań**.
5. Niektóre pliki nie znajdują się w projekcie. Aby zaimportować je jednocześnie kliknij prawym przyciskiem myszy **zasobów** folderu, **wykluczyć z projektu** innego kliknij prawym przyciskiem myszy, a następnie **zasobów** folderu, **uwzględnione w Projekt** ponownie uwzględnienie całego folderu. Wszystkie pliki z **zasobów** folderze znajdują się teraz w projekcie.

## <a name="add-the-capabilities"></a>Dodawanie funkcji
Engagement SDK wymaga prawidłowego funkcjonowania niektórych możliwości zestaw Windows SDK.

Otwórz z `Package.appxmanifest` plików i pamiętaj, że następujące funkcje są deklarowane jako:

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Inicjowanie usługi Engagement SDK
### <a name="engagement-configuration"></a>Konfiguracja usługi Engagement
Konfiguracja zaangażowania jest scentralizowana w `Resources\EngagementConfiguration.xml` pliku projektu.

Edytuj ten plik, aby określić:

* Parametry połączenia aplikacji między tagami `<connectionString>` i `<\connectionString>`.

Jeśli chcesz określić je w czasie wykonywania, należy wywołać metodę następujące przed zainicjowaniem agenta usługi Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Ciąg połączenia dla aplikacji jest wyświetlana w portalu Azure.

### <a name="engagement-initialization"></a>Inicjowania usługi Engagement
Podczas tworzenia nowego projektu `App.xaml.cs` plik został wygenerowany. Ta klasa dziedziczy `Application` i zawiera wiele metod ważne. Będzie można również używany do inicjowania usługi Engagement SDK.

Modyfikowanie `App.xaml.cs`:

* Dodaj do Twojej `using` instrukcji:
  
      using Microsoft.Azure.Engagement;
* Zdefiniuj metodę udostępniania inicjowania usługi Engagement raz dla wszystkich wywołań:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Wywołanie `InitEngagement` w `OnLaunched` metody:
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* Gdy aplikacja jest uruchamiana za pomocą schematu niestandardowego, inna aplikacja lub wiersza polecenia, a następnie `OnActivated` metoda jest wywoływana. Należy również inicjowania usługi Engagement SDK, po aktywowaniu aplikacji. Aby to zrobić, należy zastąpić `OnActivated` metody:
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> Firma Microsoft zdecydowanie zniechęcić można dodać inicjowania usługi Engagement w innym miejscu aplikacji.
> 
> 

## <a name="basic-reporting"></a>Podstawowe raportowanie
### <a name="recommended-method-overload-your-page-classes"></a>Zalecana metoda: przeciążenia sieci `Page` klas
Aby aktywować raportu wszystkie dzienniki wymagane przez zaangażowania można obliczyć użytkowników, sesji, działania, awarii (Crash) i statystyki technicznych, możesz po prostu wprowadzić wszystkie Twoje `Page` klasy podrzędne dziedziczą `EngagementPage` klasy.

Oto przykład tego, jak to zrobić w strony aplikacji. Możesz to zrobić to samo dla wszystkich stron w aplikacji.

#### <a name="c-source-file"></a>Plik źródłowy języka C#
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
> Jeśli strona zastępuje metodę `OnNavigatedTo`, nie zapomnij wywołać metody `base.OnNavigatedTo(e)`. W przeciwnym razie działanie nie zostanie zgłoszone (element `EngagementPage` wywołuje metodę `StartActivity` wewnątrz jego metody `OnNavigatedTo`).
> 
> 

#### <a name="xaml-file"></a>Plik XAML
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

#### <a name="override-the-default-behaviour"></a>Zastąpienie zachowania domyślnego
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
Jeśli nie możesz lub nie było przeciążyć Twojej `Page` klas, zamiast tego można uruchomić działań przez wywołanie metody `EngagementAgent` metody bezpośrednio.

Firma Microsoft zaleca, aby wywołać `StartActivity` wewnątrz sieci `OnNavigatedTo` metody na stronie.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Upewnij się, że prawidłowo zakończyć sesję.
> 
> Uniwersalny zestaw SDK systemu Windows automatycznie wywołuje `EndActivity` metody, gdy aplikacja zostanie zamknięta. W związku z tym jest **wysokiej** zaleca, aby wywołać `StartActivity` metody zmianie aktywności użytkownika i **nigdy** wywołać `EndActivity` metody, ta metoda wysyła do serwera usługi Engagement który bieżący użytkownik ma pozostawić aplikacji, to zostanie ma wpływ na wszystkie dzienniki aplikacji.
> 
> 

## <a name="advanced-reporting"></a>Zaawansowane raportowanie
Opcjonalnie, może zajść potrzeba raport aplikacji określonych zdarzeń, błędów i zadań, w tym celu należy użyć innych metod znalezione w `EngagementAgent` klasy. Interfejsu API usługi Engagement pozwala na korzystanie ze wszystkich zaawansowanych możliwości usługi Engagement.

Aby uzyskać więcej informacji, zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji uniwersalnych systemu Windows](mobile-engagement-windows-store-use-engagement-api.md).

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
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Tryb serii
Domyślnie raporty usługi Engagement rejestruje się w czasie rzeczywistym. Jeśli aplikacja zgłasza dzienniki bardzo często, lepiej jest buforu dzienniki i raportuj je w całości na regularne podstawy czasu (jest to nazywane "tryb serii").

Aby to zrobić, należy wywołać metodę:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argument jest wartością w **milisekund**. W dowolnym momencie Jeśli chcesz ponownie uaktywnić rejestrowania w czasie rzeczywistym, po prostu Wywołaj metodę bez parametrów lub z wartością 0.

Tryb serii nieco zwiększyć czas pracy baterii, ale ma wpływ na monitorowanie usługi Engagement: wszystkie czas trwania sesji i zadania zostanie zaokrąglony próg serii (w związku z tym sesji i zadania krótsze niż próg serii może nie być widoczna). Zaleca się przy użyciu progu w serii się niż 30000 (30s). Masz wiedzieć, który zapisano dzienniki są ograniczone do 300 elementów. W przypadku wysyłania jest zbyt długa może spowodować utratę niektórych dzienników.

> [!WARNING]
> Próg serii nie można skonfigurować do okresu mniejsza od wartości 1. Jeśli użytkownik spróbuje to zrobić, zestaw SDK wyświetli śledzenia z powodu błędu i będzie automatycznie resetować wartość domyślna, czyli 0s. Spowoduje to wyzwolenie zestaw SDK, aby zgłosić dzienniki w czasie rzeczywistym.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

