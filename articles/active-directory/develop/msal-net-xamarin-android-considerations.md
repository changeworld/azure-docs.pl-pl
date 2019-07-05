---
title: Zagadnienia dotyczące platformy Xamarin Android (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się więcej o tu konkretne uwagi przy użyciu platformy Xamarin Android Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357c83cfd0ae3fed8b13419e72f50fcb90c04186
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550650"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Zagadnienia specyficzne dla platformy Xamarin Android przy użyciu platformy MSAL.NET
W tym artykule omówiono tu konkretne uwagi, korzystając z platformy Xamarin Android przy użyciu Biblioteka Microsoft Authentication Library dla platformy .NET (platformy MSAL.NET).

Ten artykuł jest przeznaczony dla platformy MSAL.NET 3.x. Jeśli interesują Cię platformy MSAL.NET 2.x, zobacz [charakterystykę Xamarin dla systemu Android w platformy MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Ustaw działania nadrzędnego

Na platformy Xamarin.Android należy ustawić działania nadrzędnego, tak aby token pobiera ponownie, gdy miało miejsce interakcja.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Zapewnienie kontroli powraca do biblioteki MSAL raz interaktywne części kończy się przepływ uwierzytelniania
W systemie Android, musisz przesłonić `OnActivityResult` metody `Activity` i wywołać metodę SetAuthenticationContinuationEventArgs klasy biblioteki MSAL AuthenticationContinuationHelper.

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
Tego wiersza gwarantuje, że formant powraca do biblioteki MSAL po zakończył się interakcyjne część przepływu uwierzytelniania.

## <a name="update-the-android-manifest"></a>Aktualizowanie manifestu systemu Android
`AndroidManifest.xml` Powinien zawierać następujące wartości:
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

## <a name="use-the-embedded-web-view-optional"></a>Użyj widoku osadzone w sieci web (opcjonalnie)

Domyślnie platformy MSAL.NET używa przeglądarki sieci web systemu, która umożliwia logowanie Jednokrotne z aplikacji sieci Web i innych aplikacji. W rzadkich przypadkach możesz chcieć określić przy użyciu widoku osadzone w sieci web. Aby uzyskać więcej informacji, zobacz [platformy MSAL.NET używa przeglądarki sieci Web](msal-net-web-browsers.md) i [przeglądarki systemu Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Po utworzeniu nowej aplikacji platformy Xamarin.Forms i Dodaj odwołanie do pakietu NuGet platformy MSAL.Net, to po prostu działa.
Jednak jeśli chcesz uaktualnić istniejącą aplikację platformy Xamarin.Forms platformy MSAL.NET 1.1.2 w wersji zapoznawczej lub później mogą wystąpić problemy z kompilacją.

Aby rozwiązać te problemy, należy:
- Aktualizuj istniejący pakiet NuGet platformy MSAL.NET do podglądu platformy MSAL.NET 1.1.2 lub nowszy
- Sprawdź, czy zestaw narzędzi Xamarin.Forms ma być automatycznie aktualizowane do wersji 2.5.0.122203 (Jeśli nie, aktualizacja do tej wersji)
- Sprawdź, czy Xamarin.Android.Support.v4 automatycznie zaktualizowane do wersji 25.4.0.2 (Jeśli nie, aktualizacja do tej wersji)
- Wszystkie pakiety Xamarin.Android.Support powinny być przeznaczone dla wersji 25.4.0.2
- Wyczyść odbudowy
- Spróbuj ustawienie max równoległych projektów opiera się na 1 w programie Visual Studio (Opcje -> Projekty i rozwiązania -> kompilacji i uruchom -> maksymalna liczba kompilacji równoległych projektów)
- Alternatywnie Jeśli kompilujesz z wiersza polecenia, spróbuj usunąć /m z polecenia, jeśli jest ona używana.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Błąd: Nazwa "AuthenticationContinuationHelper" nie istnieje w bieżącym kontekście

Jest to prawdopodobnie spowodowane programu Visual Studio nie poprawnie zaktualizować pliku Android.csproj*. Czasami  **\<ścieżkę HintPath >** filepath zawiera nieprawidłowo netstandard13 zamiast **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Kolejne kroki

Bardziej szczegółowe informacje i przykłady znajdują się w [Android tu konkretne uwagi](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) akapitu w pliku readme.md w następującym przykładzie:

| Sample | Platforma | Opis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Prosta aplikacja Xamarin Forms przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania konta Microsoft i usługi Azure AD za pośrednictwem punktu końcowego v2.0 AADD oraz dostęp do programu Microsoft Graph przy użyciu powstały token. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |