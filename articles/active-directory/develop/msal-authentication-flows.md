---
title: Przepływy uwierzytelniania MSAL | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o przepływach uwierzytelniania i dotacjach używanych przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050508"
---
# <a name="authentication-flows"></a>Przepływy uwierzytelniania

W tym artykule opisano różne przepływy uwierzytelniania dostarczane przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).  Przepływy te mogą być używane w różnych scenariuszach aplikacji.

| Ruch | Opis | Stosowany w|  
| ---- | ----------- | ------- | 
| [Interaktywne](#interactive) | Pobiera tokenu za pośrednictwem procesu interaktywnego, który monituje użytkownika o poświadczenia za pośrednictwem przeglądarki lub okna podręcznego. | [Aplikacje klasyczne](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md) |
| [Dotacja niejawna](#implicit-grant) | Umożliwia aplikacji uzyskanie tokenów bez wykonywania wymiany poświadczeń serwera zaplecza. Dzięki temu aplikacja do logowania użytkownika, obsługi sesji i uzyskać tokeny do innych interfejsów API sieci web, wszystko w kodzie JavaScript klienta.| [Aplikacje jednostronicowe (SPA)](scenario-spa-overview.md) |
| [Kod autoryzacji](#authorization-code) | Używane w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak internetowe interfejsy API. Dzięki temu można dodać dostęp do logowania i interfejsu API do aplikacji mobilnych i klasycznych. | [Aplikacje klasyczne,](scenario-desktop-overview.md) [aplikacje mobilne,](scenario-mobile-overview.md) [aplikacje internetowe](scenario-web-app-call-api-overview.md) | 
| [W imieniu](#on-behalf-of) | Aplikacja wywołuje usługę lub internetowy interfejs API, który z kolei musi wywołać inną usługę lub internetowy interfejs API. Chodzi o propagowanie tożsamości delegowanego użytkownika i uprawnień za pośrednictwem łańcucha żądań. | [Interfejsy API sieci Web](scenario-web-api-call-api-overview.md) |
| [Poświadczenia klienta](#client-credentials) | Umożliwia dostęp do zasobów hostowanych w sieci Web przy użyciu tożsamości aplikacji. Powszechnie używane do interakcji między serwerami, które muszą działać w tle, bez natychmiastowej interakcji z użytkownikiem. | [Aplikacje demona](scenario-daemon-overview.md) |
| [Kod urządzenia](#device-code) | Umożliwia użytkownikom logowanie się do urządzeń o ograniczonym dostępie, takich jak telewizor Smart TV, urządzenie IoT lub drukarka. | [Aplikacje komputerowe/mobilne](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Zintegrowane uwierzytelnianie systemu Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Umożliwia aplikacjom w domenie lub usłudze Azure Active Directory (Azure AD) przyłączonych do komputerów uzyskanie tokenu w trybie dyskretnym (bez żadnej interakcji interfejsu użytkownika.| [Aplikacje komputerowe/mobilne](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nazwa użytkownika/hasło](scenario-desktop-acquire-token.md#username-and-password) | Umożliwia aplikacji zalogowanie się użytkownika przez bezpośrednie obsługę hasła. Ten przepływ nie jest zalecany. | [Aplikacje komputerowe/mobilne](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody
 
W zależności od sposobu zbudowanego klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft.  Przepływy te mogą tworzyć różne tokeny (id_tokens, odświeżanie tokenów, tokeny dostępu), a także kody autoryzacji i wymagają różnych tokenów, aby je działały. Ten wykres zawiera omówienie:
 
|Ruch | Wymaga | id_token | token dostępu | token odświeżania | kod autoryzacyjny | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Niejawny przepływ](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Odświeżanie wykorzystania tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token odświeżania | x | x | x| |
|[Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md) | token dostępu| x| x| x| |
|[Przepływ kodu urządzenia](v2-oauth2-device-code.md) | | x| x| x| |
|[Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |
 
Tokeny wystawione w trybie niejawnym mają ograniczenie długości ze względu na `response_mode` `query` przekazywanie z powrotem do przeglądarki za pośrednictwem adresu URL (gdzie jest lub `fragment`).  Niektóre przeglądarki mają limit rozmiaru adresu URL, który można umieścić na pasku przeglądarki i zakończyć się niepowodzeniem, gdy jest zbyt długi.  W związku z tym `groups` tokeny te nie mają lub `wids` roszczeń.

## <a name="interactive"></a>Interaktywne

Msal obsługuje możliwość interakcyjnie monitować użytkownika o ich poświadczenia, aby zalogować się i uzyskać token przy użyciu tych poświadczeń.

![Diagram przepływu interaktywnego](media/msal-authentication-flows/interactive.png)

Aby uzyskać więcej informacji na temat używania MSAL.NET do interaktywnego pozyskiwania tokenów na określonych platformach, zobacz:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Platforma uniwersalna systemu Windows](msal-net-uwp-considerations.md)

Aby uzyskać więcej informacji na temat połączeń interaktywnych w pliku MSAL.js, zobacz [Szybkie zachowanie w interakcyjnych żądaniach MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Dotacja niejawna

MSAL obsługuje [OAuth 2 niejawny przepływ dotacji](v2-oauth2-implicit-grant-flow.md), który umożliwia aplikacji, aby uzyskać tokeny z platformy tożsamości firmy Microsoft bez wykonywania wymiany poświadczeń serwera zaplecza. Dzięki temu aplikacja do logowania użytkownika, obsługi sesji i uzyskać tokeny do innych interfejsów API sieci web, wszystko w kodzie JavaScript klienta.

![Diagram niejawnego przepływu dotacji](media/msal-authentication-flows/implicit-grant.svg)

Wiele nowoczesnych aplikacji internetowych jest zbudowanych jako aplikacje jednostronicowe po stronie klienta, napisane przy użyciu języka JavaScript lub struktury SPA, takiej jak Angular, Vue.js i React.js. Te aplikacje są uruchamiane w przeglądarce sieci web i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci web po stronie serwera. Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym logowanie się do użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub interfejsów API sieci Web przy użyciu niejawnego przepływu dotacji. Przepływ niejawny umożliwia aplikacji, aby uzyskać tokeny identyfikatora do reprezentowania uwierzytelnionego użytkownika, a także tokeny dostępu potrzebne do wywołania chronionych interfejsów API.

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które używają międzyplatformowych struktur JavaScript, takich jak Electron i React-Native, ponieważ wymagają one dalszych możliwości interakcji z platformami macierzystymi.

## <a name="authorization-code"></a>Kod autoryzacji

MSAL obsługuje [oauth 2 kod autoryzacji grant](v2-oauth2-auth-code-flow.md). Ta dotacja może służyć w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak internetowe interfejsy API. Dzięki temu można dodać dostęp do logowania i interfejsu API do aplikacji mobilnych i klasycznych. 

Gdy użytkownicy logują się do aplikacji sieci Web (witryn sieci Web), aplikacja sieci web otrzymuje kod autoryzacji.  Kod autoryzacji jest realizowany w celu uzyskania tokenu do wywoływania internetowych interfejsów API. W ASP.NET i ASP.NET podstawowych aplikacji sieci web `AcquireTokenByAuthorizationCode` jedynym celem jest dodanie tokenu do pamięci podręcznej tokenu. Token może być następnie używany przez aplikację (zwykle w kontrolerach, które po `AcquireTokenSilent`prostu uzyskać token dla interfejsu API przy użyciu).

![Diagram przepływu kodu autoryzacji](media/msal-authentication-flows/authorization-code.png)

Na poprzednim diagramie aplikacja:

1. Żąda kodu autoryzacji, który jest wymieniany na token dostępu.
2. Używa tokenu dostępu do wywoływania internetowego interfejsu API.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Kodu autoryzacji można użyć tylko raz, aby zrealizować token. Nie próbuj uzyskać tokenu wiele razy z tym samym kodem autoryzacji (jest jawnie zabronione przez specyfikację standardu protokołu). Jeśli zrealizujesz kod kilka razy celowo lub nie wiesz, że struktura również robi to za Ciebie, otrzymasz następujący błąd:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Jeśli piszesz ASP.NET lub ASP.NET core aplikacji, może się to zdarzyć, jeśli nie powiesz struktury, że już zrealizowane kodu autoryzacji. W tym celu należy `context.HandleCodeRedemption()` wywołać `AuthorizationCodeReceived` metodę obsługi zdarzeń.

- Należy unikać udostępniania tokenu dostępu z ASP.NET, co może uniemożliwić poprawne działanie przyrostowej zgody. Aby uzyskać więcej informacji, zobacz [#693 problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>W imieniu

MSAL obsługuje [przepływ uwierzytelniania OAuth 2 w imieniu](v2-oauth2-on-behalf-of-flow.md).  Ten przepływ jest używany, gdy aplikacja wywołuje usługę lub internetowy interfejs API, który z kolei musi wywołać inną usługę lub internetowy interfejs API. Chodzi o propagowanie tożsamości delegowanego użytkownika i uprawnień za pośrednictwem łańcucha żądań. Aby usługa warstwy środkowej była zabezpieczona do usługi podrzędnej, musi zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft w imieniu użytkownika.

![Schemat przepływu w imieniu](media/msal-authentication-flows/on-behalf-of.png)

Na powyższym diagramie:

1. Aplikacja uzyskuje token dostępu dla internetowego interfejsu API.
2. Klient (web, desktop, mobile lub jednostronicowy interfejs API) wywołuje chroniony interfejs API sieci web, dodając token dostępu jako token na okaziciela w nagłówku uwierzytelniania żądania HTTP. Internetowy interfejs API uwierzytelnia użytkownika.
3. Gdy klient wywołuje internetowy interfejs API, internetowy interfejs API żąda innego tokenu w imieniu użytkownika.  
4. Chroniony interfejs API sieci web używa tego tokenu do wywoływania podrzędnego interfejsu API sieci web w imieniu użytkownika.  Internetowy interfejs API może również później żądać tokenów dla innych interfejsów API podrzędnego (ale nadal w imieniu tego samego użytkownika).

## <a name="client-credentials"></a>Poświadczenia klienta

MSAL obsługuje [przepływ poświadczeń klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Ten przepływ umożliwia dostęp do zasobów hostowanych w sieci Web przy użyciu tożsamości aplikacji. Ten typ dotacji jest powszechnie używany do interakcji między serwerami, które muszą być uruchamiane w tle, bez natychmiastowej interakcji z użytkownikiem. Tego typu aplikacje są często określane jako demony lub konta usług. 

Przepływ poświadczeń klienta zezwala usłudze sieci web (klientowi poufnemu) na używanie własnych poświadczeń zamiast personifikowania użytkownika do uwierzytelniania podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługą sieci web warstwy środkowej, usługą demona lub witryną sieci Web. Aby uzyskać wyższy poziom pewności, platforma tożsamości firmy Microsoft umożliwia również usłudze wywołującej używanie certyfikatu (zamiast udostępnionego klucza tajnego) jako poświadczenia.

> [!NOTE]
> Przepływ klienta poufnego nie jest dostępny na platformach mobilnych (UWP, Xamarin.iOS i Xamarin.Android), ponieważ obsługują one tylko publiczne aplikacje klienckie. Publiczne aplikacje klienckie nie wiedzą, jak udowodnić tożsamość aplikacji dostawcy tożsamości. Bezpieczne połączenie można osiągnąć na podstawie kopii zapasowej aplikacji sieci web lub interfejsu API sieci Web, wdrażając certyfikat.

MSAL.NET obsługuje dwa typy poświadczeń klienta. Te poświadczenia klienta muszą być zarejestrowane w usłudze Azure AD. Poświadczenia są przekazywane do konstruktorów poufnej aplikacji klienckiej w kodzie.

### <a name="application-secrets"></a>Tajemnice aplikacji

![Diagram poufnego klienta z hasłem](media/msal-authentication-flows/confidential-client-password.png)

Na poprzednim diagramie aplikacja:

1. Uzyskuje token przy użyciu poświadczenia klucza tajnego aplikacji lub hasła.
2. Używa tokenu do żądania zasobu.

### <a name="certificates"></a>Certyfikaty

![Schemat poufnego klienta z certyfikatem](media/msal-authentication-flows/confidential-client-certificate.png)

Na poprzednim diagramie aplikacja:

1. Uzyskuje token przy użyciu poświadczeń certyfikatu.
2. Używa tokenu do żądania zasobu.

Te poświadczenia klienta muszą być:
- Zarejestrowana w usłudze Azure AD.
- Przekazywane przy budowie poufnej aplikacji klienckiej w kodzie.

## <a name="device-code"></a>Kod urządzenia

Msal obsługuje [przepływ kodu urządzenia OAuth 2,](v2-oauth2-device-code.md)który umożliwia użytkownikom logowanie się do urządzeń o ograniczonym dostępie, takich jak smart TV, urządzenie IoT lub drukarka. Uwierzytelnianie interaktywne za pomocą usługi Azure AD wymaga przeglądarki sieci Web. Przepływ kodu urządzenia umożliwia użytkownikowi korzystanie z innego urządzenia (na przykład innego komputera lub telefonu komórkowego) do interakcyjnego logowania, gdzie urządzenie lub system operacyjny nie udostępnia przeglądarki internetowej.

Za pomocą przepływu kodu urządzenia, aplikacja uzyskuje tokeny za pośrednictwem dwuetapowego procesu specjalnie zaprojektowane dla tych urządzeń lub systemów operacyjnych. Przykładami takich aplikacji są te uruchomione na urządzeniach IoT lub narzędzia wiersza polecenia (CLI). 

![Diagram przepływu kodu urządzenia](media/msal-authentication-flows/device-code.png)

Na powyższym diagramie:

1. Za każdym razem, gdy wymagane jest uwierzytelnianie użytkownika, aplikacja udostępnia kod i prosi użytkownika o użycie innego urządzenia (takiego jak smartfon podłączony do Internetu), aby przejść do adresu URL (na przykład `https://microsoft.com/devicelogin`). Użytkownik jest następnie monitowany o wprowadzenie kodu i przechodzi przez normalne środowisko uwierzytelniania, w tym monity o zgodę i uwierzytelnianie wieloskładnikowe, jeśli to konieczne.

2. Po pomyślnym uwierzytelnieniu aplikacja wiersza polecenia odbiera wymagane tokeny za pośrednictwem kanału wstecz i używa ich do wykonywania wywołań interfejsu API sieci web, których potrzebuje.

### <a name="constraints"></a>Ograniczenia

- Przepływ kodu urządzenia jest dostępny tylko w publicznych aplikacjach klienckich.
- Urząd przekazany podczas konstruowania aplikacji klienta publicznego musi być jedną z następujących czynności:
  - Dzierżawiony `https://login.microsoftonline.com/{tenant}/` (formularza, `{tenant}` w którym jest identyfikator GUID reprezentujący identyfikator dzierżawy lub domenę skojarzoną z dzierżawcą).
  - Dla każdej pracy i`https://login.microsoftonline.com/organizations/`kont szkolnych ( ).
- Konta osobiste firmy Microsoft nie są jeszcze obsługiwane przez punkt końcowy usługi Azure AD `/common` `/consumers` w wersji 2.0 (nie można używać dzierżawy).

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Usługa MSAL obsługuje zintegrowane uwierzytelnianie systemu Windows (IWA) dla aplikacji klasycznych lub przenośnych uruchamianych na komputerze z systemem Windows przyłączonego do domeny lub usługi Azure AD. Za pomocą IWA, te aplikacje mogą uzyskać token dyskretnie (bez interakcji interfejsu użytkownika od użytkownika). 

![Diagram zintegrowanego uwierzytelniania systemu Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Na poprzednim diagramie aplikacja:

1. Uzyskuje token przy użyciu zintegrowanego uwierzytelniania systemu Windows.
2. Używa tokenu do żądania zasobu.

### <a name="constraints"></a>Ograniczenia

IWA obsługuje tylko użytkowników federowanych, co oznacza, że użytkownicy utworzyli w usłudze Active Directory i będą wspierani przez usługę Azure AD. Użytkownicy utworzyli bezpośrednio w usłudze Azure AD bez kopii zapasowej usługi Active Directory (użytkownicy zarządzani) nie mogą używać tego przepływu uwierzytelniania. To ograniczenie nie ma wpływu na [przepływ nazwy użytkownika/hasła.](#usernamepassword)

IWA jest przeznaczony dla aplikacji napisanych dla platform .NET Framework, .NET Core i Universal Windows Platform.

IWA nie pomija uwierzytelniania wieloskładnikowego. Jeśli skonfigurowano uwierzytelnianie wieloskładnikowe, IWA może zakończyć się niepowodzeniem, jeśli wymagane jest wyzwanie uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe wymaga interakcji z użytkownikiem.

Nie masz kontroli, gdy dostawca tożsamości żąda uwierzytelniania dwuskładnikowego do wykonania. Administrator dzierżawy nie. Zazwyczaj uwierzytelnianie dwuskładnikowe jest wymagane podczas logowania się z innego kraju, gdy nie masz połączenia za pośrednictwem sieci VPN z siecią firmową, a czasami nawet wtedy, gdy masz połączenie za pośrednictwem sieci VPN. Usługa Azure AD używa sztucznej inteligencji do ciągłego uczenia się, czy wymagane jest uwierzytelnianie dwuskładnikowe. Jeśli IWA nie powiedzie się, należy wrócić do [interaktywny monit użytkownika] (#interactive).

Urząd przekazany podczas konstruowania aplikacji klienta publicznego musi być jedną z następujących czynności:
- Dzierżawiony `https://login.microsoftonline.com/{tenant}/` (formularza, `tenant` w którym jest identyfikator guid reprezentujący identyfikator dzierżawy lub domenę skojarzoną z dzierżawcą).
- Dla każdej pracy i`https://login.microsoftonline.com/organizations/`kont szkolnych ( ). Konta osobiste Firmy Microsoft nie są obsługiwane `/common` `/consumers` (nie można używać ani dzierżawy).

Ponieważ IWA jest cichy przepływ, jeden z następujących musi być true:
- Użytkownik aplikacji musi wcześniej wyrazić zgodę na korzystanie z aplikacji. 
- Administrator dzierżawy musi wcześniej wyrazić zgodę na korzystanie z aplikacji przez wszystkich użytkowników w dzierżawie.

Oznacza to, że jedna z następujących elementów jest prawdziwa:
- Jako deweloper wybrałeś **Grant** w witrynie Azure portal dla siebie.
- Administrator dzierżawy wybrał **opcję Przyznanie/odwołanie zgody administratora dla {domeny dzierżawy}** na karcie **Uprawnienia interfejsu API** rejestracji aplikacji (zobacz Dodawanie [uprawnień dostępu do interfejsów API sieci](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)Web ).
- Użytkownik wyraził możliwość wyrażenia zgody na aplikację (patrz [Żądanie zgody poszczególnych użytkowników).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- Administrator dzierżawy wyraził zgodę na aplikację (patrz [zgoda administratora).](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

Przepływ IWA jest włączony dla aplikacji .NET desktop, .NET Core i Windows Universal Platform. W programie .NET Core należy podać nazwę użytkownika do IWA, ponieważ program .NET Core nie może uzyskać nazw użytkowników z systemu operacyjnego.
  
Aby uzyskać więcej informacji na temat zgody, zobacz [uprawnienia i zgoda w wersji 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nazwa użytkownika/hasło

MSAL obsługuje [OAuth 2 resource owner password credentials grant](v2-oauth-ropc.md), który umożliwia aplikacji zalogować się użytkownika przez bezpośrednie obsługiwanie hasła. W aplikacji komputerowej można użyć przepływu nazwy użytkownika/hasła, aby uzyskać token w trybie cichym. Podczas korzystania z aplikacji nie jest wymagany żaden interfejs użytkownika.

![Diagram przepływu nazwy użytkownika/hasła](media/msal-authentication-flows/username-password.png)

Na poprzednim diagramie aplikacja:

1. Uzyskuje token, wysyłając nazwę użytkownika i hasło do dostawcy tożsamości.
2. Wywołuje internetowy interfejs API przy użyciu tokenu.

> [!WARNING]
> Ten przepływ nie jest zalecany. Wymaga wysokiego stopnia zaufania i ekspozycji użytkownika.  Tego przepływu należy używać tylko wtedy, gdy nie można używać innych, bezpieczniejszych przepływów. Aby uzyskać więcej informacji, zobacz [Co jest rozwiązaniem narastającego problemu haseł?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Preferowanym przepływem do pochyłego uzyskiwania tokenu na komputerach przyłączonych do domeny systemu Windows jest [zintegrowane uwierzytelnianie systemu Windows.](#integrated-windows-authentication) W przeciwnym razie można również użyć [przepływu kodu urządzenia](#device-code).

Mimo że jest to przydatne w niektórych przypadkach (scenariusze DevOps), jeśli chcesz użyć nazwy użytkownika/hasła w interaktywnych scenariuszach, w których udostępniasz własny interfejs użytkownika, spróbuj go uniknąć. Używając nazwy użytkownika/hasła:
- Użytkownicy, którzy muszą wykonać uwierzytelnianie wieloskładnikowe, nie będą mogli się zalogować (ponieważ nie ma interakcji).
- Użytkownicy nie będą mogli samodzielnie logować się.

### <a name="constraints"></a>Ograniczenia

Oprócz [ograniczeń zintegrowanego uwierzytelniania systemu Windows](#integrated-windows-authentication)obowiązują również następujące ograniczenia:

- Przepływ nazwy użytkownika/hasła nie jest zgodny z dostępem warunkowym i uwierzytelnianiem wieloskładnikowym. W związku z tym jeśli aplikacja jest uruchamiana w dzierżawie usługi Azure AD, w której administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie można użyć tego przepływu. Wiele organizacji to robi.
- Działa tylko na kontach służbowych i szkolnych (nie kontach Microsoft).
- Przepływ jest dostępny na pulpicie .NET i .NET Core, ale nie na platformie uniwersalnej systemu Windows.

### <a name="azure-ad-b2c-specifics"></a>Szczegóły usługi Azure AD B2C

Aby uzyskać więcej informacji na temat korzystania z MSAL.NET i usługi Azure AD B2C, zobacz [Korzystanie z ROPC z usługą Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
