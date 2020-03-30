---
title: Użyj brokerów z Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować aplikacje platformy Xamarin dla systemu iOS, które mogą używać programu Microsoft Authenticator i Biblioteki uwierzytelniania Firmy Microsoft dla platformy .NET (MSAL.NET). Dowiedz się również, jak przeprowadzić migrację z biblioteki uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) do biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262791"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Korzystanie z aplikacji Microsoft Authenticator lub Intune Company Portal w aplikacjach platformy Xamarin

W systemach Android i iOS brokerzy, tacy jak Microsoft Authenticator i portal firmy microsoft intune specyficzne dla systemu Android, umożliwiają:

- **Logowanie jednokrotne (Logowanie jednokrotne):** Użytkownicy nie muszą logować się do każdej aplikacji.
- **Identyfikacja urządzenia**: Broker uzyskuje dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest połączony z miejscem pracy.
- **Weryfikacja identyfikacji aplikacji:** Gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL.

Aby włączyć jedną z tych `WithBroker()` funkcji, należy `PublicClientApplicationBuilder.CreateApplication` użyć parametru podczas wywoływania metody. Parametr `.WithBroker()` jest domyślnie ustawiony na true. 

Skorzystaj również z instrukcji podanych w poniższych sekcjach, aby skonfigurować uwierzytelnianie brokerskie dla aplikacji systemu [iOS](#brokered-authentication-for-ios) lub aplikacji [systemu Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Uwierzytelnianie brokerskie dla systemu iOS

Aby umożliwić aplikacji Xamarin.iOS, aby rozmawiać z aplikacją [Microsoft Authenticator,](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) należy wykonać następujące czynności.

### <a name="step-1-enable-broker-support"></a>Krok 1: Włącz obsługę brokera
Należy włączyć obsługę brokera dla `PublicClientApplication`poszczególnych wystąpień . Obsługa jest domyślnie wyłączona. Podczas tworzenia `PublicClientApplication` `PublicClientApplicationBuilder`za pośrednictwem `WithBroker()` , należy użyć parametru, jak pokazano w poniższym przykładzie. Parametr `WithBroker()` jest domyślnie ustawiony na true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Krok 2: Włącz dostęp do pęku kluczy

Aby włączyć dostęp do pęku kluczy, musisz mieć grupę dostępu pęku kluczy dla aplikacji. Za pomocą `WithIosKeychainSecurityGroup()` interfejsu API można ustawić grupę dostępu pęku kluczy podczas tworzenia aplikacji:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do pęku kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: Aktualizacja AppDelegate do obsługi wywołania zwrotnego
Gdy Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) wywołuje brokera, broker wywołuje z powrotem do aplikacji za pomocą `OpenUrl` metody `AppDelegate` klasy. Ponieważ MSAL czeka na odpowiedź brokera, aplikacja musi współpracować, aby wywołać MSAL.NET z powrotem. Aby włączyć tę współpracę, zaktualizuj plik, `AppDelegate.cs` aby zastąpić następującą metodę.

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

Ta metoda jest wywoływana przy każdym uruchomieniu aplikacji. Jest on używany jako okazja do przetwarzania odpowiedzi od brokera i zakończenia procesu uwierzytelniania, który MSAL.NET rozpoczęty.

### <a name="step-4-set-uiviewcontroller"></a>Krok 4: Ustaw UIViewController()
Nadal w `AppDelegate.cs` pliku, należy ustawić okno obiektu. Zwykle dla systemu Xamarin iOS nie trzeba ustawiać okna obiektu. Ale potrzebujesz okna obiektu, aby wysyłać i odbierać odpowiedzi od brokera. 

Aby skonfigurować okno obiektu, 
1. W `AppDelegate.cs` pliku ustawiono `App.RootViewController` nowy `UIViewController()`plik . To przypisanie zapewnia, że wywołanie brokera zawiera `UIViewController`. Jeśli to ustawienie jest przypisane niepoprawnie, może pojawić się ten błąd:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Podczas `AcquireTokenInteractive` wywołania `.WithParentActivityOrWindow(App.RootViewController)` użyj, a następnie przekaż odwołanie do okna obiektu, którego użyjesz.

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

### <a name="step-5-register-a-url-scheme"></a>Krok 5: Zarejestruj schemat adresu URL
MSAL.NET używa adresów URL do wywoływania brokera, a następnie zwraca odpowiedź brokera do aplikacji. Aby ukończyć podróż w obie strony, zarejestruj `Info.plist` schemat adresu URL aplikacji w pliku.

Nazwa `CFBundleURLSchemes` musi `msauth.` zawierać jako prefiks. Postępuj zgodnie `CFBundleURLName`z prefiksem z . 

W schemacie `BundleId` adresu URL jednoznacznie `$"msauth.(BundleId)"`identyfikuje aplikację: . Więc `BundleId` jeśli `com.yourcompany.xforms`jest , to `msauth.com.yourcompany.xforms`schemat adresu URL jest .

> [!NOTE]
> Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po otrzymaniu odpowiedzi od brokera.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 6: Dodaj identyfikator brokera do sekcji LSApplicationQueriesSchemes

MSAL używa `–canOpenURL:` do sprawdzenia, czy broker jest zainstalowany na urządzeniu. W systemie iOS 9 firma Apple zablokowała schematy, o które może wypytyć aplikacja. 

Dodaj `msauthv2` do `LSApplicationQueriesSchemes` sekcji `Info.plist` pliku, jak w poniższym przykładzie:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Krok 7: Zarejestruj identyfikator URI przekierowania w portalu aplikacji

Podczas korzystania z brokera, przekierowanie identyfikator URI ma dodatkowe wymagania. Identyfikator URI przekierowania _musi_ mieć następujący format:

```csharp
$"msauth.{BundleId}://auth"
```

Oto przykład:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Należy zauważyć, że identyfikator `CFBundleURLSchemes` URI przekierowania `Info.plist` pasuje do nazwy, która została uwzględniona w pliku.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 8: Upewnij się, że identyfikator URI przekierowania jest zarejestrowany w aplikacji

Identyfikator URI przekierowania musi być zarejestrowany w [portalu rejestracji aplikacji](https://portal.azure.com) jako prawidłowy identyfikator URI przekierowania dla aplikacji. 

Portal rejestracji aplikacji zapewnia nowe środowisko, które ułatwia obliczanie identyfikatora URI odpowiedzi brokera z identyfikatora pakietu. 

Aby obliczyć identyfikator URI przekierowania:

1. W portalu rejestracji aplikacji wybierz pozycję **Uwierzytelnianie** > **Wypróbuj nowe środowisko**.

   ![Wypróbuj nowe środowisko rejestracji aplikacji](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Wybierz **pozycję Dodaj platformę**.

   ![Dodawanie platformy](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Gdy lista platform jest obsługiwana, wybierz **iOS**.

   ![Konfigurowanie systemu iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Wprowadź identyfikator pakietu zgodnie z życzeniem, a następnie wybierz pozycję **Konfiguruj**.

   ![Wprowadź identyfikator pakietu](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po wykonaniu czynności identyfikator URI przekierowania jest obliczany dla Ciebie.

![Kopiowanie identyfikatora URI przekierowania](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Uwierzytelnianie brokerskie dla systemu Android

### <a name="step-1-enable-broker-support"></a>Krok 1: Włącz obsługę brokera

Obsługa brokera jest włączona na podstawie aplikacji per-PublicClientApplication. Jest domyślnie wyłączona. Podczas `WithBroker()` tworzenia parametru `IPublicClientApplication` `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizacja AppDelegate do obsługi wywołania zwrotnego

Gdy MSAL.NET wywołuje brokera, broker z kolei oddzwoni do aplikacji za pomocą metody OnActivityResult(). Ponieważ MSAL będzie czekać na odpowiedź od brokera, aplikacja musi kierować wynik do MSAL.NET.
Można to osiągnąć, kierując wynik do `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` metody OnActivityResult(), jak pokazano poniżej

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Ta metoda jest wywoływana za każdym razem, gdy aplikacja brokera jest uruchamiana i jest używana jako okazja do przetworzenia odpowiedzi od brokera i zakończenia procesu uwierzytelniania rozpoczętego przez MSAL.NET.

### <a name="step-3-set-an-activity"></a>Krok 3: Ustawianie aktywności

Aby uwierzytelnianie brokerskie działało, musisz ustawić działanie, aby usługa MSAL mogła wysyłać i odbierać odpowiedzi od brokera.

Aby to zrobić, musisz podać działanie (zwykle MainActivity) `WithParentActivityOrWindow(object parent)` do obiektu nadrzędnego jako. 

**Na przykład:**

W wywołaniu nabyć token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Krok 4: Zarejestruj redirecturi w portalu aplikacji

Usługa MSAL używa adresów URL do wywoływania brokera, a następnie powrotu do aplikacji. Aby ukończyć tę podróż w obie strony, musisz zarejestrować schemat adresu URL dla aplikacji. Ten identyfikator URI przekierowania musi być zarejestrowany w portalu rejestracji aplikacji usługi Azure AD jako prawidłowy identyfikator URI przekierowania dla aplikacji.


Identyfikator URI przekierowania wymagany dla aplikacji jest zależny od certyfikatu używanego do podpisywania pliku APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Ostatnia część identyfikatora `hgbUYHVBYUTvuvT&Y6tr554365466=`URI , jest podpis, który apk jest podpisany z base64 zakodowane.
Jednak w fazie rozwoju aplikacji przy użyciu programu Visual Studio, jeśli debugowanie kodu bez podpisywania apk z określonym certyfikatem, Visual Studio podpisze apk dla Ciebie do celów debugowania, nadając APK unikatowy podpis dla maszyny, na których jest zbudowany. W związku z tym za każdym razem, gdy tworzysz aplikację na innym komputerze, należy zaktualizować identyfikator URI przekierowania w kodzie aplikacji i rejestracji aplikacji w witrynie Azure portal w celu uwierzytelnienia za pomocą usługi MSAL. 

Podczas debugowania może wystąpić wyjątek MSAL (lub komunikat dziennika) stwierdzający, że podany identyfikator URI przekierowania jest niepoprawny. **Ten wyjątek zapewni również identyfikator URI przekierowania, który powinien być używany** z bieżącego komputera, na których debugujesz. Można użyć tego identyfikatora URI przekierowania, aby kontynuować rozwój na razie.

Gdy będziesz gotowy do sfinalizowania kodu, należy zaktualizować identyfikator URI przekierowania w kodzie i rejestracji aplikacji w witrynie Azure Portal, aby użyć podpisu certyfikatu, z którego będzie podpisywanie pliku APK.

W praktyce oznacza to, że musisz zarejestrować identyfikator URI przekierowania dla każdego członka zespołu oraz identyfikator URI przekierowania dla wersji podpisanej przez produkcję pliku APK.

Podpis można również obliczyć samodzielnie, podobnie jak robi to msal: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Istnieje również możliwość nabycia podpisu dla pakietu za pomocą keytool z następującymi poleceniami:

Dla systemu Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Dla komputerów Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Następne kroki

Dowiedz się [więcej o zagadnieniach dotyczących korzystania z platformy uniwersalnej systemu Windows z MSAL.NET](msal-net-uwp-considerations.md).
