---
title: Zagadnienia dotyczące systemu Android platformy Xamarin (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o zagadnieniach dotyczących korzystania z systemu Android platformy Xamarin z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132515"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Zagadnienia dotyczące korzystania z systemu Android platformy Xamarin z MSAL.NET
W tym artykule omówiono, co należy wziąć pod uwagę podczas korzystania z systemu Android platformy Xamarin z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Ustawianie działania nadrzędnego

W systemie Android platformy Xamarin ustaw działanie nadrzędne, tak aby token powraca po interakcji. Oto przykład kodu:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

W biuletynie MSAL 4.2 lub nowszym można `PublicClientApplication`również ustawić tę funkcję na poziomie . Aby to zrobić, użyj wywołania zwrotnego:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Jeśli używasz [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) `PublicClientApplication` , a następnie kod konstruktora wygląda jak w poniższym przykładzie.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Upewnij się, że kontrola powraca do MSAL 
Po zakończeniu interaktywnej części przepływu uwierzytelniania upewnij się, że formant powraca do msal. W systemie Android należy `OnActivityResult` zastąpić `Activity`metodę . Następnie wywołaj `SetAuthenticationContinuationEventArgs` metodę `AuthenticationContinuationHelper` klasy MSAL. 

Oto przykład:

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

Ten wiersz zapewnia, że formant powraca do MSAL na końcu interaktywnej części przepływu uwierzytelniania.

## <a name="update-the-android-manifest"></a>Aktualizowanie manifestu systemu Android
Plik *AndroidManifest.xml* powinien zawierać następujące wartości:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Zastąp nazwę pakietu zarejestrowaną w `android:host=` witrynie Azure portal dla wartości. Zastąp wartość skrótu klucza zarejestrowanego w witrynie `android:path=` Azure portal. Skrót podpisu *nie* powinien być zakodowany w adresie URL. Upewnij się, że`/`na początku skrótu podpisu pojawi się wiodący ukośnik do przodu ( ) ( ) ( ) ( ) ( )

Alternatywnie [utwórz działanie w kodzie,](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) a nie ręcznie edytuje *system AndroidManifest.xml*. Aby utworzyć działanie w kodzie, najpierw `Activity` utwórz klasę, która zawiera atrybut i `IntentFilter` atrybut. 

Oto przykład klasy reprezentującej wartości pliku XML:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Formy manifestu 4.3.X

Xamarin.Forms 4.3.x generuje kod, `package` który `com.companyname.{appName}` ustawia atrybut w *AndroidManifest.xml*. Jeśli `DataScheme` używasz `msal{client_id}`jako , możesz zmienić wartość, aby `MainActivity.cs` dopasować wartość obszaru nazw.

## <a name="use-the-embedded-web-view-optional"></a>Korzystanie z osadzonego widoku sieci Web (opcjonalnie)

Domyślnie MSAL.NET korzysta z systemowej przeglądarki internetowej. Ta przeglądarka umożliwia uzyskanie logowania jednokrotnego za pomocą aplikacji sieci web i innych aplikacji. W rzadkich przypadkach system może używać osadzonego widoku sieci Web. 

W tym przykładzie kodu pokazano, jak skonfigurować osadzony widok sieci Web:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Aby uzyskać więcej informacji, zobacz [Używanie przeglądarek internetowych do MSAL.NET](msal-net-web-browsers.md) i [xamarin android zagadnienia dotyczące przeglądarki systemu](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Można utworzyć nową aplikację Xamarin.Forms i dodać odwołanie do MSAL.NET pakietu NuGet.
Ale może mieć problemy z kompilacją, jeśli uaktualnienie istniejącej aplikacji Xamarin.Forms do MSAL.NET wersji zapoznawczej 1.1.2 lub nowszej.

Aby rozwiązać problemy z kompilacją:

- Zaktualizuj istniejący pakiet NuGet MSAL.NET, aby MSAL.NET wersji zapoznawczej 1.1.2 lub nowszej.
- Sprawdź, czy xamarin.forms automatycznie zaktualizowane do wersji 2.5.0.122203. W razie potrzeby zaktualizuj xamarin.forms do tej wersji.
- Sprawdź, czy plik Xamarin.Android.Support.v4 jest automatycznie aktualizowany do wersji 25.4.0.2. W razie potrzeby zaktualizuj wersję 25.4.0.2.
- Upewnij się, że wszystkie pakiety Xamarin.Android.Support docelowej wersji 25.4.0.2.
- Wyczyścić lub odbudować aplikację.
- W programie Visual Studio spróbuj skonfigurować maksymalną liczbę równoległych kompilacji projektu na 1. Aby to zrobić, wybierz **opcje** > **Projekty i rozwiązania Kompilacja** > i**Uruchom** > **maksymalną liczbę równoległych kompilacji projektów**.
- Jeśli budujesz z wiersza polecenia i `/m`używa polecenia, spróbuj usunąć ten element z polecenia.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Błąd: nazwa AuthenticationContinuationHelper nie istnieje w bieżącym kontekście

Jeśli błąd wskazuje, `AuthenticationContinuationHelper` że nie istnieje w bieżącym kontekście, Visual Studio może niepoprawnie zaktualizować plik Android.csproj*. Czasami * \<HintPath>* ścieżka pliku niepoprawnie zawiera *netstandard13* zamiast *monoandroid90*.

W tym przykładzie znajduje się poprawna ścieżka pliku:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz przykładową [aplikację mobilną platformy Xamarin, która korzysta z platformy tożsamości firmy Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). W poniższej tabeli podsumowano istotne informacje w pliku README.

| Sample | Platforma | Opis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Prosta aplikacja Xamarin.Forms, która pokazuje, jak używać usługi MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem punktu końcowego usługi Azure AD 2.0. Aplikacja pokazuje również, jak uzyskać dostęp do programu Microsoft Graph i pokazuje wynikowy token. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
