---
title: Konfigurowanie aplikacji mobilnych, które wywołują interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132483"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurowanie aplikacji mobilnej, która wywołuje interfejsy API sieci Web

Po utworzeniu aplikacji dowiesz się, jak skonfigurować kod przy użyciu parametrów rejestracji aplikacji. Aplikacje mobilne składają się z pewnych złożoności związanych z montażem do ich struktury tworzenia.

## <a name="find-msal-support-for-mobile-apps"></a>Znajdź obsługę MSAL dla aplikacji mobilnych

Następujące typy bibliotek uwierzytelniania firmy Microsoft (MSAL) obsługują aplikacje mobilne.

BIBLIOTEKA MSAL | Opis
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Służy do tworzenia aplikacji przenośnych. Program MSAL.NET obsługuje następujące platformy do kompilowania aplikacji mobilnej: platforma uniwersalna systemu Windows (platformy UWP), Xamarin. iOS i Xamarin. Android.
![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Służy do tworzenia natywnych aplikacji systemu iOS przy użyciu języka C lub SWIFT.
![MSAL. Systemów](media/sample-v2-code/logo_android.png) <br/> MSAL. Systemów | Służy do tworzenia natywnych aplikacji systemu Android w języku Java dla systemu Android.

## <a name="instantiate-the-application"></a>Tworzenie wystąpienia aplikacji

### <a name="android"></a>Android

Aplikacje mobilne używają klasy `PublicClientApplication`. Oto jak utworzyć jego wystąpienie:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Aplikacje mobilne w systemie iOS muszą tworzyć wystąpienia klasy `MSALPublicClientApplication`. Aby utworzyć wystąpienie klasy, użyj poniższego kodu. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Dodatkowe właściwości MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) mogą zastąpić domyślny urząd, określić identyfikator URI przekierowania lub zmienić zachowanie buforowania tokenów MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin lub platformy UWP

W tej sekcji wyjaśniono, jak utworzyć wystąpienie aplikacji dla platformy Xamarin. iOS, Xamarin. Android i platformy UWP.

#### <a name="instantiate-the-application"></a>Tworzenie wystąpienia aplikacji

W środowisku Xamarin lub platformy UWP Najprostszym sposobem tworzenia wystąpienia aplikacji jest użycie następującego kodu. W tym kodzie `ClientId` jest identyfikatorem GUID zarejestrowanej aplikacji.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Dodatkowe metody `With<Parameter>` określają nadrzędny interfejs użytkownika, przesłaniasz domyślny urząd, określ nazwę i wersję klienta dla telemetrii, określ identyfikator URI przekierowania i określ fabrykę HTTP do użycia. Fabryka HTTP może być używana na przykład do obsługi serwerów proxy i określania danych telemetrycznych i rejestrowania. 

Poniższe sekcje zawierają więcej informacji na temat tworzenia wystąpienia aplikacji.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Określ nadrzędny interfejs użytkownika, okno lub działanie

W systemie Android należy przekazać działanie nadrzędne przed uruchomieniem interakcyjnego uwierzytelniania. W systemie iOS w przypadku korzystania z brokera należy zalogować się `ViewController`. W ten sam sposób na platformy UWP można chcieć przekazać okno nadrzędne. Przekazujesz go podczas uzyskiwania tokenu. Ale podczas tworzenia aplikacji można także określić wywołanie zwrotne jako delegata, który zwraca `UIParent`.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

W systemie Android zalecamy używanie [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin). Wynikowy kod konstruktora `PublicClientApplication` wygląda następująco:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Znajdź więcej parametrów budowania aplikacji

Aby zapoznać się z listą wszystkich metod dostępnych w `PublicClientApplicationBuilder`, zobacz [listę metod](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Opis wszystkich opcji, które są dostępne w `PublicClientApplicationOptions`można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Zadania dla platformy Xamarin iOS

Jeśli używasz MSAL.NET na platformie Xamarin iOS, wykonaj następujące zadania.

* [Zastąp i zaimplementuj funkcję `OpenUrl` w `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Włącz grupy pęku kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Włącz udostępnianie pamięci podręcznej tokenu](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Włącz dostęp do łańcucha kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Aby uzyskać więcej informacji, zobacz temat [zagadnienia dotyczące platformy Xamarin dla systemu iOS](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Zadania dla MSAL dla systemów iOS i macOS

Te zadania są niezbędne w przypadku używania MSAL dla systemów iOS i macOS:

* [Implementowanie wywołania zwrotnego `openURL`](#brokered-authentication-for-msal-for-ios-and-macos)
* [Włączanie grup dostępu pęku kluczy](howto-v2-keychain-objc.md)
* [Dostosuj przeglądarki i WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Zadania dla platformy Xamarin. Android

W przypadku korzystania z platformy Xamarin. Android wykonaj następujące zadania:

- [Upewnij się, że formant wraca do MSAL po zakończeniu interaktywnej części przepływu uwierzytelniania](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aktualizowanie manifestu systemu Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Użyj osadzonego widoku sieci Web (opcjonalnie)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Rozwiązywanie problemów w razie potrzeby](msal-net-xamarin-android-considerations.md#troubleshoot)

Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące platformy Xamarin. Android](msal-net-xamarin-android-considerations.md).

Aby zapoznać się z uwagami dotyczącymi przeglądarek w systemie Android, zobacz [zagadnienia specyficzne dla platformy Xamarin. Android w programie MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Zadania dla platformy UWP

W systemie platformy UWP można używać sieci firmowych. W poniższych sekcjach opisano zadania, które należy wykonać w scenariuszu firmy.

Aby uzyskać więcej informacji, zobacz [uwagi dotyczące platformy UWP z MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurowanie aplikacji do korzystania z brokera

W systemach Android i iOS brokerzy umożliwiają włączenie:

- **Logowanie**jednokrotne: możesz użyć logowania jednokrotnego dla urządzeń zarejestrowanych w usłudze Azure Active Directory (Azure AD). W przypadku korzystania z logowania jednokrotnego użytkownicy nie muszą logować się do poszczególnych aplikacji.
- **Identyfikacja urządzenia**: to ustawienie włącza zasady dostępu warunkowego, które są powiązane z urządzeniami usługi Azure AD. Proces uwierzytelniania używa certyfikatu urządzenia, który został utworzony, gdy urządzenie zostało dołączone do miejsca pracy.
- **Weryfikacja identyfikacji aplikacji**: gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Następnie Broker weryfikuje.

### <a name="enable-the-broker-on-xamarin"></a>Włączanie brokera w środowisku Xamarin

Aby włączyć brokera w środowisku Xamarin, użyj `WithBroker()` parametru podczas wywoływania metody `PublicClientApplicationBuilder.CreateApplication`. Domyślnie `.WithBroker()` ma wartość true. 

Aby włączyć uwierzytelnianie obsługiwane przez brokera dla platformy Xamarin. iOS, wykonaj kroki opisane w [sekcji Xamarin. iOS](#enable-brokered-authentication-for-xamarin-ios) w tym artykule.

### <a name="enable-the-broker-for-msal-for-android"></a>Włączanie brokera dla MSAL dla systemu Android

Aby uzyskać informacje na temat włączania brokera w systemie Android, zobacz [uwierzytelnianie obsługiwane przez brokera w systemie Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Włączanie brokera dla MSAL dla systemów iOS i macOS

Uwierzytelnianie obsługiwane przez brokera jest domyślnie włączone dla scenariuszy usługi Azure AD w MSAL dla systemów iOS i macOS. 

Poniższe sekcje zawierają instrukcje dotyczące konfigurowania aplikacji na potrzeby obsługi uwierzytelniania obsługiwanego przez brokera dla MSAL dla platformy Xamarin. iOS lub MSAL dla systemów iOS i macOS. W dwóch zestawach instrukcji niektóre kroki są różne.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Włączanie uwierzytelniania obsługiwanego przez brokera dla platformy Xamarin iOS

Wykonaj kroki opisane w tej sekcji, aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Krok 1. Włączanie obsługi brokera

Obsługa brokera jest domyślnie wyłączona. Można ją włączyć dla poszczególnych klas `PublicClientApplication`. Użyj `WithBroker()` parametru podczas tworzenia klasy `PublicClientApplication` za pomocą `PublicClientApplicationBuilder`. Parametr `WithBroker()` jest domyślnie ustawiony na wartość true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2. Aktualizacja AppDelegate do obsługi wywołania zwrotnego

Gdy MSAL.NET wywołuje brokera, Broker następnie wywołuje z powrotem do aplikacji. Wywołuje z powrotem przy użyciu metody `AppDelegate.OpenUrl`. Ponieważ MSAL czeka na odpowiedź z brokera, aplikacja musi współpracować, aby wywoływać MSAL.NET z powrotem. To zachowanie można skonfigurować przez zaktualizowanie pliku `AppDelegate.cs` w celu przesłania metody, jak pokazano w poniższym kodzie.

```csharp
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

Ta metoda jest wywoływana za każdym razem, gdy aplikacja jest uruchamiana. Jest to okazja do przetworzenia odpowiedzi z brokera i ukończenia procesu uwierzytelniania, który MSAL.NET uruchomiony.

#### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3. Ustawianie elementu UIViewController ()

W przypadku platformy Xamarin iOS zazwyczaj nie trzeba ustawiać okna obiektu. Ale w tym przypadku należy ustawić go tak, aby można było wysyłać i odbierać odpowiedzi z brokera. Aby ustawić okno obiektu, w `AppDelegate.cs`należy ustawić `ViewController`.

Aby ustawić okno obiektu, wykonaj następujące kroki:

1. W `AppDelegate.cs`Ustaw `App.RootViewController` na nowy `UIViewController()`. To ustawienie zapewnia, że połączenie z brokerem zawiera `UIViewController`. Jeśli nie jest ustawiona poprawnie, może zostać wyświetlony następujący błąd:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Na `AcquireTokenInteractive` wywołaniu użyj `.WithParentActivityOrWindow(App.RootViewController)`. Przekaż odwołanie do okna obiektu, którego będziesz używać. Oto przykład:

    W pliku `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    W pliku `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    W `AcquireToken` Wywołaj:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Krok 4. rejestrowanie schematu adresu URL

MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera z powrotem do aplikacji. Aby zakończyć rundy, zarejestruj schemat adresu URL aplikacji w pliku `Info.plist`. 

Aby zarejestrować schemat adresu URL aplikacji, wykonaj następujące kroki:

1. Prefiks `CFBundleURLSchemes` z `msauth`. 
1. Dodaj `CFBundleURLName` na końcu. Postępuj zgodnie z tym wzorcem: 

   `$"msauth.(BundleId)"`

   W tym miejscu `BundleId` jednoznacznie identyfikuje urządzenie. Na przykład jeśli `BundleId` jest `yourcompany.xforms`, schemat adresu URL zostanie `msauth.com.yourcompany.xforms`.
    
   > [!NOTE]
   > Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po odebraniu odpowiedzi brokera.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Krok 5. Dodawanie do sekcji LSApplicationQueriesSchemes

MSAL używa `–canOpenURL:`, aby sprawdzić, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 program Apple zablokował schematy, dla których aplikacja może wykonać zapytanie.

Dodaj `msauthv2` do sekcji `LSApplicationQueriesSchemes` pliku `Info.plist`, jak w poniższym przykładzie kodu:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Uwierzytelnianie obsługiwane przez brokera dla MSAL dla systemów iOS i macOS

Uwierzytelnianie obsługiwane przez brokera jest domyślnie włączone dla scenariuszy usługi Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Krok 1. aktualizowanie AppDelegate w celu obsługi wywołania zwrotnego

Gdy MSAL dla usługi iOS i macOS wywołuje brokera, Broker wywołuje z powrotem do aplikacji za pomocą metody `openURL`. Ponieważ MSAL czeka na odpowiedź z brokera, aplikacja musi współpracować, aby wywoływać MSAL. Skonfiguruj tę możliwość, aktualizując plik `AppDelegate.m`, aby zastąpić metodę, jak pokazano w poniższym przykładzie kodu.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Jeśli przyjęto `UISceneDelegate` w systemie iOS 13 lub nowszym, zamiast tego należy umieścić wywołanie zwrotne MSAL w `scene:openURLContexts:` `UISceneDelegate`. MSAL `handleMSALResponse:sourceApplication:` musi być wywoływana tylko raz dla każdego adresu URL.
>
> Aby uzyskać więcej informacji, zobacz [dokumentację firmy Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Krok 2. rejestrowanie schematu adresu URL

MSAL dla systemów iOS i macOS używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera do aplikacji. Aby zakończyć rundy, zarejestruj schemat adresu URL aplikacji w pliku `Info.plist`.

Aby zarejestrować schemat dla aplikacji: 

1. Oznacz niestandardowy schemat adresu URL za pomocą `msauth`. 

1. Dodaj identyfikator pakietu na końcu schematu. Postępuj zgodnie z tym wzorcem: 

   `$"msauth.(BundleId)"`

   W tym miejscu `BundleId` jednoznacznie identyfikuje urządzenie. Na przykład jeśli `BundleId` jest `yourcompany.xforms`, schemat adresu URL zostanie `msauth.com.yourcompany.xforms`.
  
   > [!NOTE]
   > Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po odebraniu odpowiedzi brokera. Upewnij się, że identyfikator URI przekierowania w formacie `msauth.(BundleId)://auth` jest zarejestrowany dla aplikacji w [Azure Portal](https://portal.azure.com).
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Krok 3. Dodawanie LSApplicationQueriesSchemes

Dodaj `LSApplicationQueriesSchemes`, aby zezwolić na wywołania aplikacji Microsoft Authenticator, jeśli jest zainstalowana.

> [!NOTE]
> Schemat `msauthv3` jest potrzebny, gdy aplikacja jest kompilowana przy użyciu Xcode 11 i nowszych. 

Oto przykład sposobu dodawania `LSApplicationQueriesSchemes`:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Uwierzytelnianie obsługiwane przez brokera dla platformy Xamarin. Android

MSAL.NET nie obsługuje brokerów dla systemu Android.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu](scenario-mobile-acquire-token.md)
