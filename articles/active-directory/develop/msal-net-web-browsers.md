---
title: Korzystanie z przeglądarek internetowych (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o konkretnych zagadnieniach podczas korzystania z systemu Android platformy Xamarin z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262739"
---
# <a name="using-web-browsers-msalnet"></a>Korzystanie z przeglądarek internetowych (MSAL.NET)

Przeglądarki internetowe są wymagane do uwierzytelniania interaktywnego. Domyślnie MSAL.NET obsługuje [systemowa przeglądarka internetowa](#system-web-browser-on-xamarinios-xamarinandroid) na platformach Xamarin.iOS i Xamarin.Android. Ale [można również włączyć osadzoną przeglądarkę sieci Web](#enable-embedded-webviews-on-ios-and-android) w zależności od wymagań (UX, potrzeba logowania jednokrotnego (SSO), zabezpieczeń) w [aplikacjach Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) i [Xamarin.Android.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) Możesz nawet [dynamicznie wybrać](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) przeglądarkę internetową, której chcesz użyć na podstawie obecności Chrome lub przeglądarki obsługującej niestandardowe karty Chrome w Androidzie. MSAL.NET obsługuje tylko przeglądarkę system w aplikacjach komputerowych .NET Core.

## <a name="web-browsers-in-msalnet"></a>Przeglądarki internetowe w MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interakcja odbywa się w przeglądarce sieci Web

Ważne jest, aby zrozumieć, że podczas uzyskiwania tokenu interaktywnie, zawartość okna dialogowego nie jest dostarczana przez bibliotekę, ale przez usługę STS (Security Token Service). Punkt końcowy uwierzytelniania odsyła niektóre html i JavaScript, który kontroluje interakcję, która jest renderowana w przeglądarce sieci Web lub formancie sieci Web. Zezwolenie STS na obsługę interakcji HTML ma wiele zalet:

- Hasło (jeśli zostało wpisane) nigdy nie jest przechowywane przez aplikację ani bibliotekę uwierzytelniania.
- Umożliwia przekierowania do innych dostawców tożsamości (na przykład logowania przy za pomocą konta w szkole roboczej lub konta osobistego za pomocą usługi MSAL lub konta społecznościowego za pomocą usługi Azure AD B2C).
- Umożliwia sts kontroli dostępu warunkowego, na przykład poprzez użytkownika zrobić uwierzytelnianie wieloskładnikowe (MFA) podczas fazy uwierzytelniania (wprowadzanie pinu Windows Hello, lub wywoływane na telefonie lub w aplikacji uwierzytelniania na telefonie). W przypadkach, gdy wymagane uwierzytelnianie wieloskładnikowe nie jest jeszcze skonfigurowane, użytkownik może skonfigurować go w sam raz w tym samym oknie dialogowym.  Użytkownik wprowadza swój numer telefonu komórkowego i jest kierowany do zainstalowania aplikacji uwierzytelniania i skanowania tagu QR, aby dodać swoje konto. Ta interakcja oparta na serwerze to wspaniałe doświadczenie!
- Umożliwia użytkownikowi zmianę hasła w tym samym oknie dialogowym po wygaśnięciu hasła (podając dodatkowe pola dla starego hasła i nowego hasła).
- Umożliwia znakowanie dzierżawy lub aplikacji (obrazy) kontrolowane przez administratora dzierżawy usługi Azure AD / właściciela aplikacji.
- Umożliwia użytkownikom wyrażenie zgody na umożliwienie aplikacji dostępu do zasobów / zakresów w ich nazwie tuż po uwierzytelnieniu.

### <a name="embedded-vs-system-web-ui"></a>Wbudowany a systemowy interfejs użytkownika sieci Web

MSAL.NET jest biblioteką wieloeplatformową i ma kod specyficzny dla struktury do hostowania przeglądarki w formancie interfejsu użytkownika (na przykład w programie .Net Classic używa wersji WinForms, na platformie Xamarin używa natywnych form sterujących urządzeń przenośnych itp.). Ten formant `embedded` jest nazywany interfejsem użytkownika sieci Web. Alternatywnie, MSAL.NET jest również w stanie rozpocząć system przeglądarki systemu operacyjnego.

Ogólnie rzecz biorąc zaleca się użycie domyślnej platformy i jest to zazwyczaj przeglądarka systemowa. Przeglądarka systemowa lepiej zapamiętuje użytkowników, którzy zalogowali się wcześniej. Jeśli chcesz zmienić to zachowanie, użyj`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>W skrócie

| Framework        | Osadzone | System | Domyślne |
| ------------- |-------------| -----| ----- |
| .NET Klasyczny     | Tak | Tak^ | Osadzone |
| .NET Core     | Nie | Tak^ | System |
| .NET Standard | Nie | Tak^ | System |
| Platforma UWP | Tak | Nie | Osadzone |
| Xamarin.Android | Tak | Tak  | System |
| Xamarin.iOS | Tak | Tak  | System |
| Xamarin.Mac| Tak | Nie | Osadzone |

^ Wymagahttp://localhost" " przekierowania identyfikatora URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systemowa przeglądarka internetowa na stronie Xamarin.iOS, Xamarin.Android

Domyślnie MSAL.NET obsługuje systemowa przeglądarka internetowa na platformach Xamarin.iOS, Xamarin.Android i .NET Core. Dla wszystkich platform, które zapewniają interfejs użytkownika (czyli nie .NET Core), okno dialogowe jest dostarczane przez bibliotekę osadzania formantu przeglądarki sieci Web. MSAL.NET używa również wbudowanego widoku sieci Web dla platformy Platformy Uniwersalnej .NET Desktop i WAB. Jednak domyślnie wykorzystuje system przeglądarki **internetowej** dla aplikacji Xamarin iOS i Xamarin Android. W systemie iOS nawet wybiera widok sieci Web do użycia w zależności od wersji systemu operacyjnego (iOS12, iOS11 i starsze).

Korzystanie z przeglądarki systemowej ma znaczną zaletę dzielenia się stanem SSO z innymi aplikacjami i aplikacjami internetowymi bez konieczności brokera (portal firmy / authenticator). Przeglądarka systemowa była używana domyślnie w MSAL.NET dla platform Xamarin iOS i Xamarin Android, ponieważ na tych platformach systemowa przeglądarka internetowa zajmuje cały ekran, a środowisko użytkownika jest lepsze. Systemowego widoku sieci Web nie można odróżnić od okna dialogowego. W przypadku systemu iOS użytkownik może być jednak musiał wyrazić zgodę na oddzwonienie aplikacji przez przeglądarkę, co może być denerwujące.

## <a name="system-browser-experience-on-net-core"></a>Środowisko przeglądarki systemowej w programie .NET Core

W programie .NET Core MSAL.NET uruchomi przeglądarkę systemową jako osobny proces. MSAL.NET nie ma kontroli nad tą przeglądarką, ale po zakończeniu uwierzytelniania użytkownik jest przekierowywał stronę internetową w taki sposób, że MSAL.NET mogą przechwycić identyfikator Uri.

Można również skonfigurować aplikacje napisane dla platformy .NET Classic do korzystania z tej przeglądarki,

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET nie może wykryć, czy użytkownik odsunie się od lub po prostu zamknie przeglądarkę. Aplikacje korzystające z tej techniki są `CancellationToken`zachęcane do zdefiniowania limitu czasu (za pośrednictwem ). Zaleca się limit czasu co najmniej kilka minut, aby wziąć pod uwagę przypadki, w których użytkownik jest monitowany o zmianę hasła lub wykonanie uwierzytelniania wieloskładnikowego.

### <a name="how-to-use-the-default-os-browser"></a>Jak korzystać z domyślnej przeglądarki systemu operacyjnego

MSAL.NET musi nasłuchiwać `http://localhost:port` i przechwytywać kod wysyłany przez aad, gdy użytkownik jest gotowy na uwierzytelnianie (szczegóły można znaleźć w [kodzie autoryzacji).](v2-oauth2-auth-code-flow.md)

Aby włączyć przeglądarkę system ą:

1. Podczas rejestracji aplikacji `http://localhost` należy skonfigurować jako identyfikator przekierowania (obecnie nie obsługiwany przez B2C)
2. Podczas konstruowania PublicClientApplication, określ to przekierowanie uri:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Jeśli skonfigurujesz , wewnętrznie MSAL.NET znajdzie losowy otwarty port i użyje `http://localhost`go.

### <a name="linux-and-mac"></a>Linux i MAC

W systemie Linux MSAL.NET otworzy domyślną przeglądarkę systemu operacyjnego za pomocą narzędzia xdg-open. Aby rozwiązać problem, uruchom narzędzie na przykład z terminala,`xdg-open "https://www.bing.com"`  
Na komputerze Mac przeglądarka jest otwierana przez`open <url>`

### <a name="customizing-the-experience"></a>Dostosowywanie doświadczenia

> [!NOTE]
> Personalizacja jest dostępna w MSAL.NET 4.1.0 lub nowszym.

MSAL.NET jest w stanie odpowiedzieć komunikatem HTTP po odebraniu tokenu lub w przypadku błędu. Możesz wyświetlić wiadomość HTML lub przekierować do wybranego adresu URL:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Otwieranie określonej przeglądarki (eksperymentalne)

Możesz dostosować sposób, w jaki MSAL.NET otwiera przeglądarkę. Na przykład zamiast używać dowolnej przeglądarki, która jest domyślna, można wymusić otwarcie określonej przeglądarki:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>Platforma uniwersalna systemu Windows nie korzysta z widoku internetowego systemu

Jednak w przypadku aplikacji klasycznych uruchomienie widoku Sieci Web systemu prowadzi do doświadczenia użytkownika podrzędnego, ponieważ użytkownik widzi przeglądarkę, gdzie może już otworzyć inne karty. A gdy uwierzytelnianie miało miejsce, użytkownicy otrzymuje stronę z prośbą o zamknięcie tego okna. Jeśli użytkownik nie zwraca uwagi, może zamknąć cały proces (w tym inne karty, które nie są związane z uwierzytelnianiem). Wykorzystanie przeglądarki systemowej na pulpicie wymagałoby również otwierania portów lokalnych i nasłuchiwania na nich, co może wymagać zaawansowanych uprawnień dla aplikacji. Ty, jako deweloper, użytkownik lub administrator, możesz być niechętny temu wymaganiu.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Włączanie osadzonych widoków internetowych w systemach iOS i Android

Można również włączyć osadzone webviews w xamarin.iOS i Xamarin.Android aplikacji. Począwszy od MSAL.NET podglądu 2.0.0, MSAL.NET obsługuje również korzystanie z **wbudowanej** opcji widoku internetowego. W przypadku ADAL.NET osadzony widok internetowy jest jedyną obsługiwaną opcją.

Jako programista korzystający z MSAL.NET kierowania na xamarin możesz użyć osadzonych widoków internetowych lub przeglądarek systemowych. Jest to twój wybór w zależności od środowiska użytkownika i problemów związanych z bezpieczeństwem, na które chcesz kierować reklamy.

Obecnie MSAL.NET nie obsługuje jeszcze brokerów Android i iOS. W związku z tym, jeśli trzeba podać logowanie jednokrotne (SSO), przeglądarka systemowa może nadal być lepszym rozwiązaniem. Brokerzy obsługujący z osadzoną przeglądarką internetową są na MSAL.NET zaległości.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Różnice między osadzonym widokiem internetowym a przeglądarką system
Istnieją pewne różnice wizualne między osadzonym webview i przeglądarki systemowej w MSAL.NET.

**Interaktywne logowanie za pomocą MSAL.NET przy użyciu widoku Osadzone webview:**

![osadzona](media/msal-net-web-browsers/embedded-webview.png)

**Interaktywne logowanie za pomocą MSAL.NET za pomocą przeglądarki systemowej:**

![Przeglądarka systemowa](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opcje dla deweloperów

Jako programista korzystający z MSAL.NET masz kilka opcji wyświetlania interaktywnego okna dialogowego z usługi STS:

- **Przeglądarka systemowa.** Przeglądarka systemowa jest domyślnie ustawiona w bibliotece. Jeśli korzystasz z systemu Android, przeczytaj [przeglądarki systemowe,](msal-net-system-browser-android-considerations.md) aby uzyskać szczegółowe informacje o tym, które przeglądarki są obsługiwane do uwierzytelniania. Podczas korzystania z przeglądarki systemowej w systemie Android zalecamy, aby urządzenie miało przeglądarkę obsługującą niestandardowe karty Chrome.  W przeciwnym razie uwierzytelnianie może zakończyć się niepowodzeniem.
- **Osadzony widok internetowy.** Aby używać tylko osadzonego widoku sieci Web `AcquireTokenInteractively` w MSAL.NET, `WithUseEmbeddedWebView()` konstruktor parametrów zawiera metodę.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Wybór między osadzoną przeglądarką internetową lub przeglądarką system ą na xamarin.iOS

W aplikacji na iOS `AppDelegate.cs` można zainicjować `ParentWindow` do `null`. Nie jest używany w iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Wybór między osadzoną przeglądarką internetową lub przeglądarką system ą na Xamarin.Android

W aplikacji na `MainActivity.cs` Androida można ustawić aktywność nadrzędną, aby wynik uwierzytelniania powrócił do niej:

```csharp
 App.ParentWindow = this;
```

Następnie w: `MainPage.xaml.cs`

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Wykrywanie obecności niestandardowych kart na xamarin.Android

Jeśli chcesz użyć systemowej przeglądarki internetowej, aby włączyć SSO z aplikacjami uruchomionymi w przeglądarce, ale martwisz się o środowisko użytkownika dla `IsSystemWebViewAvailable()` urządzeń `IPublicClientApplication`z systemem Android, które nie mają przeglądarki z niestandardową obsługą kart, masz możliwość wyboru, wywołując metodę w . Ta metoda `true` zwraca, jeśli PackageManager wykrywa `false` karty niestandardowe i jeśli nie są one wykrywane na urządzeniu.

Na podstawie wartości zwróconej przez tę metodę i wymagań można podjąć decyzję:

- Można zwrócić niestandardowy komunikat o błędzie do użytkownika. Na przykład: "Zainstaluj Chrome, aby kontynuować uwierzytelnianie" -OR-
- Możesz wrócić do osadzonej opcji webview i uruchomić interfejs użytkownika jako osadzony widok internetowy.

Poniższy kod pokazuje osadzoną opcję webview:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>Program .NET Core nie obsługuje uwierzytelniania interaktywnego za pomocą wbudowanej przeglądarki

W przypadku platformy .NET Core interaktywne pozyskiwanie tokenów jest dostępne tylko za pośrednictwem systemowej przeglądarki sieci Web, a nie za pomocą osadzonych widoków sieci Web. Rzeczywiście. .NET Core nie zapewnia jeszcze interfejsu użytkownika.
Jeśli chcesz dostosować przeglądanie za pomocą systemowej przeglądarki internetowej, możesz zaimplementować interfejs [IWithCustomUI,](scenario-desktop-acquire-token.md#withcustomwebui) a nawet udostępnić własną przeglądarkę.
