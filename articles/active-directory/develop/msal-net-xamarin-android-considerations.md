---
title: Uwagi dotyczące platformy Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o określonych kwestiach dotyczących korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 678b581e09fe1eac49e4f2bf07eabbbc944c8d4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424149"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Zagadnienia specyficzne dla platformy Xamarin Android z MSAL.NET
W tym artykule omówiono określone zagadnienia dotyczące korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Ustawianie działania nadrzędnego

W środowisku Xamarin. Android należy ustawić działanie nadrzędne, aby token odwracał po wystąpieniu interakcji.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Możesz również ustawić tę wartość na poziomie PublicClientApplication (w MSAL 4.2 +) za pośrednictwem wywołania zwrotnego.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Zaleceniem jest użycie CurrentActivityPlugin [tutaj](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Następnie kod konstruktora PublicClientApplication będzie wyglądać następująco:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Upewnienie się, że kontrola wraca do MSAL po zakończeniu interaktywnej części przepływu uwierzytelniania
W systemie Android należy zastąpić metodę `OnActivityResult` `Activity` i wywołać metodę SetAuthenticationContinuationEventArgs klasy AuthenticationContinuationHelper MSAL.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Ten wiersz zapewnia, że formant wraca do MSAL po zakończeniu interaktywnej części przepływu uwierzytelniania.

## <a name="update-the-android-manifest"></a>Aktualizowanie manifestu systemu Android
`AndroidManifest.xml` powinna zawierać następujące wartości:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

Można też [utworzyć działanie w kodzie](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) , a nie edytować `AndroidManifest.xml`ręcznie. W tym celu należy utworzyć klasę, która ma atrybut `Activity` i `IntentFilter`. Klasa, która reprezentuje te same wartości powyższego kodu XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X — manifest

Kod wygenerowany przez XamarinForms 4.3. x ustawia atrybut `package` na `com.companyname.{appName}` w `AndroidManifest.xml`. Możesz chcieć zmienić wartość tak, aby była taka sama jak przestrzeń nazw `MainActivity.cs`, jeśli używasz `DataScheme` jako `msal{client_id}`.

## <a name="use-the-embedded-web-view-optional"></a>Użyj osadzonego widoku sieci Web (opcjonalnie)

Domyślnie MSAL.NET korzysta z przeglądarki sieci Web systemu, która umożliwia uzyskanie rejestracji jednokrotnej z aplikacjami sieci Web i innymi aplikacjami. W niektórych rzadkich przypadkach warto określić, że chcesz użyć osadzonego widoku sieci Web. Aby uzyskać więcej informacji, zobacz [MSAL.NET używa przeglądarki sieci Web](msal-net-web-browsers.md) i [przeglądarki systemu Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
W przypadku utworzenia nowej aplikacji platformy Xamarin. Forms i dodania odwołania do pakietu NuGet MSAL.Net będzie to możliwe.
Jeśli jednak chcesz uaktualnić istniejącą aplikację Xamarin. Forms do wersji zapoznawczej 1.1.2 lub nowszej, mogą wystąpić problemy z kompilacją.

Aby rozwiązać te problemy, należy:
- Zaktualizuj istniejący pakiet NuGet MSAL.NET do wersji MSAL.NET Preview 1.1.2 lub nowszej
- Sprawdź, czy platforma Xamarin. Forms została automatycznie zaktualizowana do wersji 2.5.0.122203 (jeśli nie, zaktualizuj do tej wersji)
- Sprawdź, czy platforma Xamarin. Android. support. v4 została automatycznie zaktualizowana do wersji 25.4.0.2 (jeśli nie, zaktualizuj do tej wersji)
- Wszystkie pakiety platformy Xamarin. Android. Support powinny mieć wersję docelową 25.4.0.2
- Wyczyść/Kompiluj ponownie
- Spróbuj ustawić maksymalną liczbę równoległych kompilacji projektu na 1 w programie Visual Studio (Opcje-> projekty i rozwiązania — > kompilacje i uruchomienia — > Maksymalna liczba kompilacji równoległych projektów)
- Alternatywnie, jeśli tworzysz z wiersza polecenia, spróbuj usunąć/m z polecenia, jeśli jest używane.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Błąd: nazwa "AuthenticationContinuationHelper" nie istnieje w bieżącym kontekście

Prawdopodobną przyczyną jest to, że program Visual Studio nie zaktualizował poprawnie pliku Android. csproj *. Czasami **\<HintPath >** FilePath nieprawidłowo zawiera netstandard13 zamiast **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Następne kroki

Więcej szczegółowych informacji i przykładów znajduje się w punkcie [uwagi dotyczące określonych](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) przykładowych plików README.MD:

| Przykład | Platforma | Opis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platformy UWP | Prosta aplikacja interfejsu Xamarin Forms, która przedstawia, jak używać MSAL do uwierzytelniania kont MSA i Azure AD za pośrednictwem punktu końcowego AADD v 2.0, a następnie uzyskiwać dostęp do Microsoft Graph przy użyciu tokenu z wynikiem. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
