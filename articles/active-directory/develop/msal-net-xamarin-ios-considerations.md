---
title: Zagadnienia dotyczące programu Xamarin dla systemu iOS (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się więcej o tu konkretne uwagi przy użyciu platformy Xamarin iOS Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
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
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602135"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin zagadnienia specyficzne dla systemu iOS przy użyciu platformy MSAL.NET
Na Xamarin iOS istnieje kilka kwestii, które należy wziąć pod uwagę podczas przy użyciu platformy MSAL.NET

- [Znane problemy z systemem iOS 12 i uwierzytelniania](#known-issues-with-ios-12-and-authentication)
- [Zastąpienia i zaimplementować `OpenUrl` działa w programach `AppDelegate`](#implement-openurl)
- [Włącz grupy łańcucha kluczy](#enable-keychain-groups)
- [Włącz udostępnianie pamięci podręcznej tokenu](#enable-token-cache-sharing-across-ios-applications)
- [Włącz dostęp do pęku kluczy](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Znane problemy z systemem iOS 12 i uwierzytelniania
Firma Microsoft wydała [biuletyn zabezpieczeń](https://github.com/aspnet/AspNetCore/issues/4647) aby podać informacje o niezgodności między iOS12 oraz niektóre typy uwierzytelniania. Niezgodność podziały ubezpieczenia, WSFed i OIDC logowania. W tym biuletynie podano też wskazówki dla deweloperów czynności można usunąć bieżące ograniczenia zabezpieczeń, dodawane przez program ASP.NET do swoich aplikacji, aby stać się zgodny z iOS12.  

Podczas tworzenia aplikacji platformy MSAL.NET na platformy Xamarin iOS, może zostać wyświetlony wejścia w nieskończoną pętlę, próbując zalogować się do witryny sieci Web z systemem iOS 12 (podobny do następującego [ADAL problem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Mogą również zawiera podział w przypadku uwierzytelniania platformy ASP.NET Core OIDC z systemem iOS 12 Safari, zgodnie z opisem w tym [problem aparatu WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementowanie OpenUrl

Najpierw musisz przesłonić `OpenUrl` metody `FormsApplicationDelegate` pochodne klasy i wywołania `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Musisz również zdefiniować schemat adresu URL, wymagają uprawnień dla aplikacji w taki sposób wywołać inną aplikację, mieć określonej formy dla adresu URL przekierowania i zarejestrować ten adres URL przekierowania w [witryny Azure portal](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Włącz grupy łańcucha kluczy

Przed token pracy w pamięci podręcznej i mieć `AcquireTokenSilentAsync` metody pracy, musi występować wiele kroków:
1. Włącz dostęp łańcucha kluczy w swojej *`* Entitlements.plist* pliku i określić **grupy łańcucha kluczy** w że identyfikator pakietu.
2. Wybierz *`*Entitlements.plist*`* w pliku **niestandardowe uprawnienia** pole okno opcji projektu dla systemu iOS **widoku podpisywania pakietu**.
3. Podczas rejestrowania certyfikatu, sprawdź, czy środowisko XCode używa tego samego identyfikatora Apple ID.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Włącz pamięć podręczną tokenu udostępnianie w aplikacjach systemu iOS

Począwszy od biblioteki MSAL 2.x, można określić grupę zabezpieczeń łańcucha kluczy do użycia na potrzeby utrwalania pamięci podręcznej tokenu dla wielu aplikacji. Dzięki temu można udostępnić pamięć podręczną tokenu między kilka aplikacji o tej samej grupy zabezpieczeń łańcucha kluczy, łącznie z tymi opracowanych przez [ADAL.NET](https://aka.ms/adal-net), aplikacji platformy Xamarin.iOS platformy MSAL.NET i aplikacji natywnych dla systemów iOS, opracowanych za pomocą [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) lub [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Udostępnianie pamięci podręcznej tokenu umożliwia logowanie jednokrotne (SSO) między wszystkie aplikacje, które używają tej samej grupy zabezpieczeń pęku kluczy.

Aby włączyć logowanie jednokrotne, należy ustawić `PublicClientApplication.iOSKeychainSecurityGroup` właściwość taką samą wartość w przypadku wszystkich aplikacji.

Na przykład przy użyciu biblioteki MSAL v3.x będzie:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Na przykład przy użyciu biblioteki MSAL v2.7.x będzie:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> `KeychainSecurityGroup` Właściwość jest przestarzała. Wcześniej w MSAL 2.x, deweloperzy zostały zmuszeni do zawierać prefiks identyfikatora zespołu, korzystając z `KeychainSecurityGroup` właściwości. 
> 
> Teraz, począwszy od biblioteki MSAL 2.7.x, biblioteka MSAL rozwiąże prefiks identyfikatora zespołu podczas wykonywania przy użyciu `iOSKeychainSecurityGroup` właściwości. Korzystając z tej właściwości, wartość nie może zawierać prefiks identyfikatora zespołu. 
> 
> Użyj nowego `iOSKeychainSecurityGroup` właściwość, która nie wymaga deweloperom udostępnianie identyfikator zespołu. `KeychainSecurityGroup` Właściwość jest obecnie przestarzała. 

## <a name="enable-keychain-access"></a>Włącz dostęp do pęku kluczy

W MSAL 2.x i biblioteki ADAL 4.x identyfikatora zespołu umożliwia dostęp do pęku kluczy, który umożliwia bibliotek uwierzytelniania w celu zapewnienia logowania jednokrotnego (SSO) między aplikacjami tego samego wydawcy. 

Co to jest [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (identyfikator zespołu)? Jest to unikatowy identyfikator (firmy lub osobiste) w Store aplikacji. Identyfikator jest unikatowy dla aplikacji. Jeśli masz więcej niż jednej aplikacji, identyfikator zespołu dla wszystkich aplikacji, będą takie same, ale identyfikator aplikacji będzie różna. Grupy dostępu łańcucha kluczy jest poprzedzony identyfikatora zespołu automatycznie dla każdej grupy przez system. To, jak system operacyjny wymusza, że aplikacje tego samego wydawcy, mają dostęp do udostępnionego łańcucha. 

Podczas inicjowania `PublicClientApplication`, jeśli zostanie wyświetlony `MsalClientException` z komunikatem: `TeamId returned null from the iOS keychain...`, należy wykonać następujące czynności w aplikacji platformy Xamarin dla systemu iOS:

1. W programie VS na karcie debugowania, przejdź do nameOfMyApp.iOS właściwości...
2. Następnie przejdź do podpisywanie pakietu systemu iOS 
3. W obszarze uprawnień niestandardowych, kliknij... i wybierz plik plik Entitlements.plist aplikacji
4. W pliku csproj aplikacji systemu iOS powinny mieć ten wiersz, teraz dołączane do: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Ponownie skompiluj** projektu.

Jest to *dodatkowo* do włączania dostępu łańcucha kluczy w `Entitlements.plist` plików, za pomocą poniżej grupy dostępu lub własnych:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Kolejne kroki

Szczegółowe informacje znajdują się w [iOS tu konkretne uwagi](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) akapitu w pliku readme.md w następującym przykładzie:

Sample | Platforma | Opis 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Prosta aplikacja Xamarin Forms przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania konta Microsoft i usługi Azure AD za pośrednictwem punktu końcowego V2.0 usługi AAD i dostęp do programu Microsoft Graph przy użyciu powstały token. <br>![Topologia](media/msal-net-xamarin-ios-considerations/topology.png)