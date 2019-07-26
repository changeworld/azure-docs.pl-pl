---
title: Aplikacja mobilna, która wywołuje interfejsy API sieci Web (Konfiguracja kodu) — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (konfigurację kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414852"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Po utworzeniu aplikacji dowiesz się, jak skonfigurować kod z parametrów aplikacji uzyskanych podczas rejestracji aplikacji. Aplikacje mobilne mają również pewne złożone informacje, które należy wykonać, aby korzystać z dołączania do struktury używanej do kompilowania tych aplikacji

## <a name="msal-libraries-supporting-mobile-apps"></a>Biblioteki MSAL obsługujące aplikacje mobilne

Biblioteki firmy Microsoft obsługujące aplikacje mobilne to:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Do tworzenia aplikacji przenośnych. MSAL.NET obsługiwane platformy do kompilowania aplikacji mobilnej to platformy UWP, Xamarin. iOS i Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Aby tworzyć natywne aplikacje dla systemu iOS z celem C lub SWIFT
  ![MSAL. Systemów](media/sample-v2-code/logo_android.png) <br/> MSAL. Systemów | Aby tworzyć natywne aplikacje systemu Android w języku Java dla systemu Android

## <a name="configuring-the-application"></a>Konfigurowanie aplikacji

Aplikacje mobilne używają `PublicClientApplication` klasy MSAL. Oto jak utworzyć jego wystąpienie:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin lub platformy UWP

W poniższym akapicie wyjaśniono, jak skonfigurować kod aplikacji dla platformy Xamarin. iOS, Xamarin. Android i platformy UWP. Pierwszym krokiem jest utworzenie wystąpienia aplikacji. Opcjonalnym krokiem jest skonfigurowanie brokera.

#### <a name="instantiating-the-application"></a>Tworzenie wystąpienia aplikacji

W środowisku Xamarin lub platformy UWP Najprostszym sposobem tworzenia wystąpienia aplikacji jest następujące, gdzie `ClientId` jest identyfikator GUID aplikacji jako zarejestrowany.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Istnieją dodatkowe metody*parametrów* , które ustawiają elementy nadrzędne interfejsu użytkownika, zastępują domyślny urząd, określ nazwę i wersję klienta (dla telemetrii), określ identyfikator URI przekierowania, określ fabrykę http do użycia (na przykład do obsługi serwerów proxy, określ dane telemetryczne i rejestrowanie). Jest to temat dotyczący następujących akapitów.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Określanie nadrzędnego interfejsu użytkownika/okna/działania

W systemie Android należy przekazać działanie nadrzędne przed uruchomieniem interakcyjnego uwierzytelniania. W przypadku korzystania z brokera w systemie iOS należy przekazać plik viewcontroller. W ten sam sposób na platformy UWP można chcieć przekazać okno nadrzędne. Jest to możliwe podczas uzyskiwania tokenu, ale można również określić wywołanie zwrotne podczas tworzenia aplikacji, delegat zwraca UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

W systemie Android zalecamy korzystanie z tego `CurrentActivityPlugin` [miejsca](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Następnie kod `PublicClientApplication` konstruktora będzie wyglądać następująco:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Więcej parametrów konstruowania aplikacji

- Aby uzyskać listę wszystkich modyfikatorów dostępnych na stronie `PublicClientApplicationBuilder`, zobacz dokumentację referencyjną [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Opis wszystkich opcji dostępnych w `PublicClientApplicationOptions` temacie zobacz [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)w dokumentacji referencyjnej

#### <a name="xamarin-ios-specific-considerations"></a>Zagadnienia specyficzne dla platformy Xamarin iOS

W oprogramowaniu Xamarin iOS istnieje kilka kwestii, które należy wziąć pod uwagę podczas korzystania z MSAL.NET:

1. [Przesłoń i zaimplementuj `OpenUrl` funkcję w`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Włącz grupy pęku kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Włącz udostępnianie pamięci podręcznej tokenu](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Włącz dostęp do łańcucha kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Szczegółowe informacje znajdują się w temacie [Xamarin iOS](msal-net-xamarin-ios-considerations.md) — zagadnienia

#### <a name="other-xamarin-android-specific-considerations"></a>Inne uwagi dotyczące platformy Xamarin dla systemu Android

Oto specyficzne dla platformy Xamarin Android:

- [Upewnienie się, że kontrola wraca do MSAL po zakończeniu interaktywnej części przepływu uwierzytelniania](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Aktualizowanie manifestu systemu Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Użyj osadzonego widoku sieci Web (opcjonalnie)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Rozwiązywanie problemów](msal-net-xamarin-android-considerations.md#troubleshooting)

Szczegółowe informacje znajdują się w [tematach dotyczących platformy Xamarin Android](msal-net-xamarin-android-considerations.md)

Wreszcie istnieją pewne specyficzne informacje o przeglądarkach w systemie Android. Objaśniono je w [zagadnieniach specyficznych dla systemu Android z MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Zagadnienia dotyczące platformy UWP

W systemie platformy UWP można używać sieci firmowych. Aby uzyskać informacje o specyficznych platformy UWPach, zobacz [zagadnienia specyficzne dla platforma uniwersalna systemu Windows z MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Konfigurowanie aplikacji do korzystania z brokera

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Dlaczego warto używać brokerów w aplikacjach Xamarin. iOS i Xamarin. Android?

W systemach Android i iOS brokerzy umożliwiają włączenie:

- Logowanie jednokrotne (SSO). Użytkownicy nie muszą logować się do poszczególnych aplikacji.
- Identyfikacja urządzenia. Włącza zasady dostępu warunkowego dotyczące urządzeń usługi Azure AD, uzyskując dostęp do certyfikatu urządzenia, który został utworzony na urządzeniu, gdy był dołączony do miejsca pracy.
- Weryfikacja identyfikacji aplikacji. Gdy aplikacja wywołuje brokera, przekazuje jego adres URL przekierowania i weryfikuje go.

### <a name="enable-the-brokers-on-xamarin"></a>Włączanie brokerów na platformie Xamarin

Aby włączyć jedną z tych funkcji, użyj `WithBroker()` parametru podczas `PublicClientApplicationBuilder.CreateApplication` wywoływania metody. `.WithBroker()`Domyślnie ma wartość true. Postępuj zgodnie z poniższymi instrukcjami dla [systemu iOS](#brokered-authentication-for-xamarinios).

### <a name="brokered-authentication-for-xamarinios"></a>Uwierzytelnianie obsługiwane przez brokera dla platformy Xamarin. iOS

Wykonaj poniższe czynności, aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Krok 1: Włącz obsługę brokera

Obsługa brokera jest włączona na`PublicClientApplication` podstawie. Jest on domyślnie wyłączony. Należy użyć `WithBroker()` parametru (domyślnie ustawione na true) podczas `PublicClientApplication` tworzenia przy użyciu `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizowanie AppDelegate w celu obsługi wywołania zwrotnego

Gdy MSAL.NET wywołuje brokera, Broker będzie z kolei wywoływać z powrotem do aplikacji za pomocą `AppDelegate.OpenUrl` metody. Ponieważ MSAL będzie oczekiwać na odpowiedź z brokera, aplikacja musi współpracować, aby wywoływać MSAL.NET z powrotem. W tym celu należy zaktualizować `AppDelegate.cs` plik, aby zastąpić poniższą metodę.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Ta metoda jest wywoływana za każdym razem, gdy aplikacja jest uruchamiana i służy jako szansa do przetwarzania odpowiedzi z brokera i ukończenia procesu uwierzytelniania zainicjowane przez MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: Ustaw UIViewController ()

W przypadku platformy Xamarin iOS zwykle nie trzeba ustawiać okna obiektu, ale w tym przypadku można wysyłać i odbierać odpowiedzi z brokera. Nadal w `AppDelegate.cs`, Ustaw plik viewcontroller.

Aby ustawić okno obiektu, wykonaj następujące czynności:

1) W `AppDelegate.cs`programie `App.RootViewController` ustaw wartość na nowy. `UIViewController()` Spowoduje to upewnienie się, że `UIViewController` istnieje połączenie z brokerem. Jeśli nie jest ustawiona poprawnie, może wystąpić następujący błąd:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) W wywołaniu AcquireTokenInteractive Użyj `.WithParentActivityOrWindow(App.RootViewController)` i przekaż odwołanie do okna obiektu, którego będziesz używać.

**Na przykład:**

W pliku `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
W pliku `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
W wywołaniu tokenu pozyskiwania:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Krok 4: Zarejestruj schemat adresu URL

MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera z powrotem do aplikacji. Aby zakończyć rundy, należy zarejestrować schemat adresu URL dla aplikacji w `Info.plist` pliku.

Oznacz jako `CFBundleURLSchemes` `msauth`prefiks. Następnie Dodaj `CFBundleURLName` do końca.

`$"msauth.(BundleId)"`

**Na przykład:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ten schemat adresu URL staje się częścią RedirectUri używany do unikatowego identyfikowania aplikacji podczas otrzymywania odpowiedzi z brokera.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>Krok 5. LSApplicationQueriesSchemes

MSAL używa `–canOpenURL:` do sprawdzenia, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 Firma Apple zablokowała schematy, dla których aplikacja może wykonać zapytanie.

**Dodaj** **`msauthv2`** do`LSApplicationQueriesSchemes` sekcji pliku`Info.plist` .

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Uwierzytelnianie obsługiwane przez brokera dla platformy Xamarin. Android

Usługa MSAL.NET nie obsługuje jeszcze brokerów dla systemu Android.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu](scenario-mobile-acquire-token.md)
