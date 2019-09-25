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
ms.openlocfilehash: e7b731c9936ab85b19428687330044a46c563c49
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268367"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Po utworzeniu aplikacji dowiesz się, jak skonfigurować kod przy użyciu parametrów rejestracji aplikacji. Aplikacje mobilne mają również pewne złożone informacje, które należy wykonać, aby korzystać z dołączania do struktury używanej do kompilowania tych aplikacji

## <a name="msal-libraries-supporting-mobile-apps"></a>Biblioteki MSAL obsługujące aplikacje mobilne

Biblioteki firmy Microsoft obsługujące aplikacje mobilne to:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Do tworzenia aplikacji przenośnych. MSAL.NET obsługiwane platformy do kompilowania aplikacji mobilnej to platformy UWP, Xamarin. iOS i Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Tworzenie natywnych aplikacji systemu iOS z celem języka C lub SWIFT
  ![MSAL. Systemów](media/sample-v2-code/logo_android.png) <br/> MSAL. Systemów | Aby tworzyć natywne aplikacje systemu Android w języku Java dla systemu Android

## <a name="instantiating-the-application"></a>Tworzenie wystąpienia aplikacji

### <a name="android"></a>Android

Aplikacje mobilne używają `PublicClientApplication` klasy. Oto jak utworzyć jego wystąpienie:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Aplikacje mobilne w systemie iOS muszą tworzyć wystąpienia `MSALPublicClientApplication` klasy.

Cel-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Adres
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Istnieją [dodatkowe właściwości MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) , które mogą zastąpić domyślny urząd, określić identyfikator URI przekierowania lub zmienić zachowanie buforowania tokenu MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin lub platformy UWP

W poniższym akapicie wyjaśniono, jak utworzyć wystąpienie aplikacji dla platformy Xamarin. iOS, Xamarin. Android i platformy UWP.

#### <a name="instantiating-the-application"></a>Tworzenie wystąpienia aplikacji

W środowisku Xamarin lub platformy UWP Najprostszym sposobem tworzenia wystąpienia aplikacji jest następująca, gdzie `ClientId` jest identyfikator GUID zarejestrowanej aplikacji.

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

W systemie Android zalecamy korzystanie z tego okna `CurrentActivityPlugin` [.](https://github.com/jamesmontemagno/CurrentActivityPlugin)  Następnie kod `PublicClientApplication` konstruktora będzie wyglądać następująco:

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

## <a name="xamarin-ios-specific-considerations"></a>Zagadnienia specyficzne dla platformy Xamarin iOS

W oprogramowaniu Xamarin iOS istnieje kilka kwestii, które należy wziąć pod uwagę podczas korzystania z MSAL.NET:

1. [Przesłoń i zaimplementuj `OpenUrl` funkcję w`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Włącz grupy pęku kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Włącz udostępnianie pamięci podręcznej tokenu](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Włącz dostęp do łańcucha kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Szczegółowe informacje znajdują się w temacie [Xamarin iOS — zagadnienia](msal-net-xamarin-ios-considerations.md)

## <a name="msal-for-ios-and-macos-specific-considerations"></a>MSAL dla konkretnych zagadnień dla systemów iOS i macOS

Podobne uwagi dotyczą korzystania z MSAL dla systemów iOS i macOS:

1. [Implementowanie `openURL` wywołania zwrotnego](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Włączanie grup dostępu pęku kluczy](howto-v2-keychain-objc.md)
3. [Dostosuj przeglądarki i WebViews](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Zagadnienia specyficzne dla platformy Xamarin Android

Oto specyficzne dla platformy Xamarin Android:

- [Upewnienie się, że kontrola wraca do MSAL po zakończeniu interaktywnej części przepływu uwierzytelniania](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Aktualizowanie manifestu systemu Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Użyj osadzonego widoku sieci Web (opcjonalnie)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Rozwiązywanie problemów](msal-net-xamarin-android-considerations.md#troubleshooting)

Szczegółowe informacje znajdują się w [tematach dotyczących platformy Xamarin Android](msal-net-xamarin-android-considerations.md)

Wreszcie istnieją pewne specyficzne informacje o przeglądarkach w systemie Android. Objaśniono je w [zagadnieniach specyficznych dla systemu Android z MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Zagadnienia dotyczące platformy UWP

W systemie platformy UWP można używać sieci firmowych. Aby uzyskać dodatkowe informacje na temat korzystania z biblioteki MSAL z platformy UWP, zobacz [zagadnienia specyficzne dla platforma uniwersalna systemu Windows przy użyciu MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Konfigurowanie aplikacji do korzystania z brokera

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Dlaczego warto używać brokerów w aplikacjach dla systemów iOS i Android?

W systemach Android i iOS brokerzy umożliwiają włączenie:

- Logowanie jednokrotne (SSO), gdy urządzenie jest zarejestrowane w usłudze AAD. Użytkownicy nie muszą logować się do poszczególnych aplikacji.
- Identyfikacja urządzenia. Włącza zasady dostępu warunkowego dotyczące urządzeń usługi Azure AD, uzyskując dostęp do certyfikatu urządzenia, który został utworzony na urządzeniu, gdy był dołączony do miejsca pracy.
- Weryfikacja identyfikacji aplikacji. Gdy aplikacja wywołuje brokera, przekazuje jego adres URL przekierowania i weryfikuje go.

### <a name="enable-the-brokers-on-xamarin"></a>Włączanie brokerów na platformie Xamarin

Aby włączyć jedną z tych funkcji, użyj `WithBroker()` parametru podczas `PublicClientApplicationBuilder.CreateApplication` wywoływania metody. `.WithBroker()`Domyślnie ma wartość true. Wykonaj poniższe kroki dla platformy [Xamarin. iOS](#brokered-authentication-for-xamarinios).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Włączanie brokera dla MSAL dla systemów iOS i macOS

Uwierzytelnianie obsługiwane przez brokera jest domyślnie włączone dla scenariuszy usługi AAD w MSAL dla systemów iOS i macOS. Wykonaj poniższe kroki, aby skonfigurować aplikację do obsługi uwierzytelniania obsługiwanego przez brokera dla [MSAL dla systemów iOS i macOS](#brokered-authentication-for-msal-for-ios-and-macos). Należy zauważyć, że niektóre kroki są różne [dla MSAL dla platformy Xamarin. iOS](#brokered-authentication-for-xamarinios) i [MSAL dla systemów iOS i macOS](#brokered-authentication-for-msal-for-ios-and-macos).

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

W przypadku platformy Xamarin iOS zazwyczaj nie trzeba ustawiać okna obiektu, ale w tym przypadku można wysyłać i odbierać odpowiedzi z brokera. Nadal w `AppDelegate.cs`, Ustaw plik viewcontroller.

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

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Uwierzytelnianie obsługiwane przez brokera dla MSAL dla systemów iOS i macOS

Uwierzytelnianie obsługiwane przez brokera jest domyślnie włączone w scenariuszach usługi AAD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Krok 1: Aktualizowanie AppDelegate w celu obsługi wywołania zwrotnego

Gdy MSAL dla usługi iOS i macOS wywołuje brokera, Broker będzie z kolei wywoływać z powrotem do aplikacji za pomocą `openURL` metody. Ponieważ MSAL będzie oczekiwać na odpowiedź z brokera, aplikacja musi współpracować, aby wywoływać MSAL z powrotem. W tym celu należy zaktualizować `AppDelegate.m` plik, aby zastąpić poniższą metodę.

Cel-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Adres

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Należy pamiętać, że w przypadku przyjęcia UISceneDelegate w systemie iOS 13 + MSAL wywołanie zwrotne musi zostać umieszczone `scene:openURLContexts:` w UISceneDelegate (zobacz [dokumentację firmy Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). MSAL `handleMSALResponse:sourceApplication:` musi być wywoływana tylko raz dla każdego adresu URL.

#### <a name="step-2-register-a-url-scheme"></a>Krok 2: Zarejestruj schemat adresu URL

MSAL dla systemów iOS i macOS używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera z powrotem do aplikacji. Aby zakończyć rundy, należy zarejestrować schemat adresu URL dla aplikacji w `Info.plist` pliku.

Oznacz niestandardowy schemat adresu URL za `msauth`pomocą. Następnie Dodaj **Identyfikator pakietu** na końcu.

`msauth.(BundleId)`

**Na przykład:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ten schemat adresu URL staje się częścią RedirectUri używany do unikatowego identyfikowania aplikacji podczas otrzymywania odpowiedzi z brokera. Upewnij się, że RedirectUri w formacie `msauth.(BundleId)://auth` jest zarejestrowany dla aplikacji w [witrynie Azure Portal](https://portal.azure.com).

```XML
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-lsapplicationqueriesschemes"></a>Krok 3: LSApplicationQueriesSchemes

**Dodaj`LSApplicationQueriesSchemes`** , aby zezwolić na wykonywanie wywołania Microsoft Authenticator, jeśli jest zainstalowana.
Należy pamiętać, że schemat "msauthv3" jest potrzebny podczas kompilowania aplikacji za pomocą Xcode 11 i nowszych. 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Uwierzytelnianie obsługiwane przez brokera dla platformy Xamarin. Android

Usługa MSAL.NET nie obsługuje jeszcze brokerów dla systemu Android.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu](scenario-mobile-acquire-token.md)
