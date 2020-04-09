---
title: Konfigurowanie aplikacji mobilnych, które wywołują internetowe interfejsy API | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API (konfiguracja kodu aplikacji)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06475b53acf6c9161888e29723feab9cdc4336d5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882730"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurowanie aplikacji mobilnej, która wywołuje internetowe interfejsy API

Po utworzeniu aplikacji dowiesz się, jak skonfigurować kod przy użyciu parametrów rejestracji aplikacji. Aplikacje mobilne przedstawiają pewne zawiłości związane z dopasowaniem się do ich struktury tworzenia.

## <a name="find-msal-support-for-mobile-apps"></a>Znajdź obsługę msal dla aplikacji mobilnych

Następujące typy biblioteki uwierzytelniania firmy Microsoft (MSAL) obsługują aplikacje mobilne.

BIBLIOTEKA MSAL | Opis
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Służy do tworzenia aplikacji przenośnych. MSAL.NET obsługuje następujące platformy do tworzenia aplikacji mobilnej: Uniwersalna platforma systemu Windows (UWP), Xamarin.iOS i Xamarin.Android.
![Plik MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> Plik MSAL.iOS | Służy do tworzenia natywnych aplikacji systemu iOS przy użyciu Objective-C lub Swift.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Służy do tworzenia natywnych aplikacji na Androida w języku Java dla systemu Android.

## <a name="instantiate-the-application"></a>Tworzenie wystąpienia aplikacji

### <a name="android"></a>Android

Aplikacje mobilne używają `PublicClientApplication` tej klasy. Oto jak utworzyć jego wystąpienie:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Aplikacje mobilne w systemu iOS muszą utworzyć wystąpienie `MSALPublicClientApplication` klasy. Aby utworzyć wystąpienie klasy, użyj następującego kodu. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Dodatkowe właściwości MSALPublicClientappientConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) można zastąpić domyślny urząd, określić identyfikator URI przekierowania lub zmienić zachowanie buforowania tokenów MSAL. 

### <a name="xamarin-or-uwp"></a>Platforma Xamarin lub platformy uniwersalnej systemu wyurzcie na platformy uniwersalnej systemu

W tej sekcji wyjaśniono, jak utworzyć wystąpienia aplikacji dla aplikacji Xamarin.iOS, Xamarin.Android i PLATFORMY UNIWERSALNEJ systemu Windows.

#### <a name="instantiate-the-application"></a>Tworzenie wystąpienia aplikacji

W języku Xamarin lub platformy uniwersalnej systemu i platformy uniwersalnej systemu współużytkowego najprostszym sposobem wystąpienia aplikacji jest użycie następującego kodu. W tym `ClientId` kodzie jest identyfikator GUID zarejestrowanej aplikacji.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Dodatkowe `With<Parameter>` metody ustawiają nadrzędny interfejs użytkownika, zastępują domyślny urząd, określają nazwę klienta i wersję danych telemetrycznych, określają identyfikator URI przekierowania i określają fabrykę HTTP do użycia. Fabryka HTTP może służyć, na przykład, do obsługi serwerów proxy i do określania danych telemetrycznych i rejestrowania. 

Poniższe sekcje zawierają więcej informacji na temat tworzenia wystąpienia aplikacji.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Określanie nadrzędnego interfejsu użytkownika, okna lub działania

W systemie Android należy przekazać działanie nadrzędne przed rozpoczęciem uwierzytelniania interaktywnego. W przypadku systemu iOS, gdy korzystasz z `ViewController`brokera, musisz przekazać plik . W ten sam sposób na platformie uniwersalnej systemu Windows można przekazać w oknie nadrzędnym. Przekazujesz go po nabyciu tokenu. Ale podczas tworzenia aplikacji, można również określić wywołania zwrotnego `UIParent`jako pełnomocnika, który zwraca .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

W systemie Android zalecamy [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)użycie . Wynikowy `PublicClientApplication` kod konstruktora wygląda następująco w tym przykładzie:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Znajdź więcej parametrów tworzenia aplikacji

Aby uzyskać listę wszystkich metod, `PublicClientApplicationBuilder`które są dostępne na , zobacz [metody listy](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Opis wszystkich opcji, które są `PublicClientApplicationOptions`widoczne w programie , zobacz [dokumentację referencyjną](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Zadania dla systemu Xamarin iOS

Jeśli używasz MSAL.NET w systemie Xamarin iOS, wykonaj następujące zadania.

* [Zastądić `OpenUrl` i zaimplementować funkcję w`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Włączanie grup pęku kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Włączanie udostępniania pamięci podręcznej tokenów](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Włączanie dostępu do pęku kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Aby uzyskać więcej informacji, zobacz zagadnienia dotyczące systemu [iOS platformy Xamarin](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Zadania dla aplikacji MSAL dla systemu iOS i macOS

Te zadania są niezbędne podczas korzystania z usługi MSAL dla systemu iOS i macOS:

* [Implementowanie `openURL` wywołania zwrotnego](#brokered-authentication-for-msal-for-ios-and-macos)
* [Włączanie grup dostępu do pęku kluczy](howto-v2-keychain-objc.md)
* [Dostosowywanie przeglądarek i widoków WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Zadania dla systemu Xamarin.Android

Jeśli używasz platformy Xamarin.Android, wykonaj następujące zadania:

- [Upewnij się, że formant powraca do msal po zakończeniu interaktywnej części przepływu uwierzytelniania](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aktualizowanie manifestu systemu Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Korzystanie z osadzonego widoku sieci Web (opcjonalnie)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Rozwiązywanie problemów w razie potrzeby](msal-net-xamarin-android-considerations.md#troubleshoot)

Aby uzyskać więcej informacji, zobacz zagadnienia dotyczące systemu [Xamarin.Android](msal-net-xamarin-android-considerations.md).

Aby uzyskać informacje na temat przeglądarek na Androida, zobacz [Xamarin.Android specyficzne zagadnienia z MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Zadania dla platformy uniwersalnej systemu i platformy uniwersalnej systemu

Na platformie UNIWERSALNEJ systemu zużycie sieci można używać sieci firmowych. W poniższych sekcjach wyjaśniono zadania, które należy wykonać w scenariuszu korporacyjnym.

Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące platformy uniwersalnej](msal-net-uwp-considerations.md)systemu z MSAL.NET .

## <a name="configure-the-application-to-use-the-broker"></a>Skonfiguruj aplikację do korzystania z brokera

Na Androida i iOS brokerzy umożliwiają:

- **Logowanie jednokrotne (Logowanie jednokrotne):** Logowania jednokrotnego można używać dla urządzeń zarejestrowanych w usłudze Azure Active Directory (Azure AD). Korzystając z logowania sytego, użytkownicy nie muszą logować się do każdej aplikacji.
- **Identyfikacja urządzenia:** To ustawienie umożliwia zasady dostępu warunkowego, które są powiązane z urządzeniami usługi Azure AD. Proces uwierzytelniania używa certyfikatu urządzenia, który został utworzony, gdy urządzenie zostało przyłączone do miejsca pracy.
- **Weryfikacja identyfikacji aplikacji:** Gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Następnie broker to weryfikuje.

### <a name="enable-the-broker-on-xamarin"></a>Włącz brokera na Xamarin

Aby włączyć brokera na xamarin, należy użyć parametru `WithBroker()` podczas wywoływania `PublicClientApplicationBuilder.CreateApplication` metody. Domyślnie `.WithBroker()` jest ustawiona na true. 

Aby włączyć uwierzytelnianie oparte na brokerze dla systemu Xamarin.iOS, wykonaj kroki opisane w [sekcji Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) w tym artykule.

### <a name="enable-the-broker-for-msal-for-android"></a>Włącz brokera dla MSAL dla Systemu Android

Aby uzyskać informacje na temat włączania brokera na Androida, zobacz [Uwierzytelnianie brokerskie w systemie Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Włączanie brokera dla msal dla systemu iOS i macOS

Uwierzytelnianie brokerskie jest domyślnie włączone dla scenariuszy usługi Azure AD w systemie MSAL dla systemu iOS i macOS. 

Poniższe sekcje zawierają instrukcje konfigurowania aplikacji dla obsługi uwierzytelniania obsługiwanego przez brokera dla usługi MSAL dla platformy Xamarin.iOS lub MSAL dla systemu iOS i macOS. W dwóch zestawach instrukcji niektóre kroki różnią się.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Włączanie uwierzytelniania z pomocą brokera dla systemu Xamarin iOS

Wykonaj kroki opisane w tej sekcji, aby włączyć aplikację Xamarin.iOS, aby porozmawiać z aplikacją [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Krok 1: Włącz obsługę brokera

Obsługa brokera jest domyślnie wyłączona. Włącz go dla `PublicClientApplication` poszczególnych klas. Użyj `WithBroker()` parametru podczas `PublicClientApplication` tworzenia `PublicClientApplicationBuilder`klasy za pośrednictwem . Parametr `WithBroker()` jest domyślnie ustawiony na true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizacja AppDelegate do obsługi wywołania zwrotnego

Gdy MSAL.NET wywołuje brokera, broker następnie wywołuje z powrotem do aplikacji. Wywołuje z powrotem `AppDelegate.OpenUrl` przy użyciu metody. Ponieważ MSAL czeka na odpowiedź od brokera, aplikacja musi współpracować, aby wywołać MSAL.NET z powrotem. Można skonfigurować to zachowanie, `AppDelegate.cs` aktualizując plik, aby zastąpić metodę, jak pokazano w poniższym kodzie.

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

Ta metoda jest wywoływana przy każdym uruchomieniu aplikacji. Jest to okazja do przetworzenia odpowiedzi od brokera i ukończenia procesu uwierzytelniania, który MSAL.NET rozpoczęty.

#### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: Ustawianie kontrolera UIViewController()

W przypadku systemu Xamarin iOS zwykle nie trzeba ustawiać okna obiektu. Ale w tym przypadku należy ustawić go tak, że można wysyłać i odbierać odpowiedzi od brokera. Aby ustawić okno obiektu, w `AppDelegate.cs` `ViewController`pliku , ustaw .

Aby ustawić okno obiektu, wykonaj następujące czynności:

1. W `AppDelegate.cs`, `App.RootViewController` ustaw nowy `UIViewController()`. To ustawienie gwarantuje, że wywołanie `UIViewController`brokera zawiera . Jeśli nie jest poprawnie ustawiona, może pojawić się ten błąd:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Podczas `AcquireTokenInteractive` rozmowy użyj `.WithParentActivityOrWindow(App.RootViewController)`. Przekaż w odwołaniu do okna obiektu, które będzie używane. Oto przykład:

    W pliku `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    W pliku `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    W `AcquireToken` wywołaniu:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Krok 4: Zarejestruj schemat adresu URL

MSAL.NET używa adresów URL do wywoływania brokera, a następnie zwraca odpowiedź brokera z powrotem do aplikacji. Aby zakończyć podróż w obie strony, zarejestruj `Info.plist` schemat adresu URL aplikacji w pliku. 

Aby zarejestrować schemat adresów URL aplikacji, wykonaj następujące czynności:

1. `CFBundleURLSchemes` Prefiks `msauth`z . 
1. Dodaj `CFBundleURLName` na końcu. Postępuj zgodnie z tym wzorcem: 

   `$"msauth.(BundleId)"`

   W `BundleId` tym miejscu jednoznacznie identyfikuje się urządzenie. Na przykład, `BundleId` `yourcompany.xforms`jeśli tak, `msauth.com.yourcompany.xforms`schemat adresu URL to .
    
   > [!NOTE]
   > Ten schemat adresu URL stanie się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po otrzymaniu odpowiedzi brokera.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: Dodaj do sekcji LSApplicationQueriesSchemes

MSAL używa `–canOpenURL:` do sprawdzenia, czy broker jest zainstalowany na urządzeniu. W systemie iOS 9 firma Apple zablokowała schematy, o które może wypytyć aplikacja.

Dodaj `msauthv2` do `LSApplicationQueriesSchemes` sekcji `Info.plist` pliku, jak w poniższym przykładzie kodu:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Uwierzytelnianie brokerskie dla usługi MSAL dla systemu iOS i macOS

Uwierzytelnianie brokerskie jest domyślnie włączone w scenariuszach usługi Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Krok 1: Aktualizacja AppDelegate do obsługi wywołania zwrotnego

Gdy MSAL dla systemu iOS i macOS wywołuje brokera, `openURL` broker wywołuje z powrotem do aplikacji przy użyciu metody. Ponieważ MSAL czeka na odpowiedź od brokera, aplikacja musi współpracować, aby oddzwonić MSAL. Skonfiguruj tę funkcję, aktualizując `AppDelegate.m` plik, aby zastąpić metodę, jak pokazują poniższe przykłady kodu.

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
> Jeśli przyjęto `UISceneDelegate` w systemie iOS 13 lub nowszym, `scene:openURLContexts:` `UISceneDelegate` zamiast tego umieść wywołanie zwrotne MSAL. Msal `handleMSALResponse:sourceApplication:` musi być wywoływana tylko raz dla każdego adresu URL.
>
> Aby uzyskać więcej informacji, zobacz [dokumentację firmy Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Krok 2: Zarejestruj schemat adresu URL

Usługa MSAL dla systemu iOS i macOS używa adresów URL do wywoływania brokera, a następnie zwracania odpowiedzi brokera do aplikacji. Aby zakończyć podróż w obie strony, zarejestruj `Info.plist` schemat adresu URL aplikacji w pliku.

Aby zarejestrować schemat aplikacji: 

1. Prefiksuj niestandardowy schemat adresów URL za pomocą `msauth`programu . 

1. Dodaj identyfikator pakietu na końcu schematu. Postępuj zgodnie z tym wzorcem: 

   `$"msauth.(BundleId)"`

   W `BundleId` tym miejscu jednoznacznie identyfikuje się urządzenie. Na przykład, `BundleId` `yourcompany.xforms`jeśli tak, `msauth.com.yourcompany.xforms`schemat adresu URL to .
  
   > [!NOTE]
   > Ten schemat adresu URL stanie się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po otrzymaniu odpowiedzi brokera. Upewnij się, że identyfikator URI przekierowania w formacie `msauth.(BundleId)://auth` jest zarejestrowany dla aplikacji w [witrynie Azure portal](https://portal.azure.com).
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Krok 3: Dodaj LSApplicationQueriesSchemes

Dodaj, `LSApplicationQueriesSchemes` aby zezwolić na wywołania z aplikacją Microsoft Authenticator, jeśli jest zainstalowana.

> [!NOTE]
> Schemat `msauthv3` jest potrzebny, gdy aplikacja jest skompilowana przy użyciu Xcode 11 i nowszych. 

Oto przykład dodawania: `LSApplicationQueriesSchemes`

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Uwierzytelnianie brokerskie dla platformy Xamarin.Android

MSAL.NET nie obsługuje brokerów dla Androida.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zdobywanie tokenu](scenario-mobile-acquire-token.md)
