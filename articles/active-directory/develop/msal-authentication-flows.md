---
title: Przepływy uwierzytelniania (Microsoft Authentication Library) | Azure
description: Więcej informacji na temat przepływów uwierzytelniania i przyznaje używane przez Microsoft Authentication Library (MSAL).
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 821143d39f8a4c06501ee38ef598a9d06d267d72
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273100"
---
# <a name="authentication-flows"></a>Przepływy uwierzytelniania

W tym artykule opisano przepływy uwierzytelniania inny, pod warunkiem Microsoft Authentication Library (MSAL).  Te przepływy może służyć w wielu różnych zastosowaniach.

| Ruch | Opis | Używane w|  
| ---- | ----------- | ------- | 
| [Interaktywne](#interactive) | Pobiera token proces interaktywny, który monituje użytkownika o poświadczenia, za pomocą przeglądarki lub okna podręcznego. | [Aplikacje komputerowe](scenario-desktop-overview.md), [aplikacji mobilnych](scenario-mobile-overview.md) |
| [Przyznawanie niejawne](#implicit-grant) | Umożliwia aplikacji uzyskiwanie tokenów bez przeprowadzania wymianę poświadczeń serwerów zaplecza. Umożliwia to aplikacji, loguje użytkownika, zachować sesję i uzyskiwanie tokenów do innych interfejsów API sieci web, wszystko to w ramach klienta kodu JavaScript.| [Aplikacji jednostronicowej (SPA)](scenario-spa-overview.md) |
| [Kod autoryzacji](#authorization-code) | Używane w aplikacjach, które są zainstalowane na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci web. Pozwala na dodawanie logowania i dostępu do interfejsu API do aplikacji mobilnych i klasycznych. | [Aplikacje komputerowe](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md), [aplikacje sieci web](scenario-web-app-call-api-overview.md) | 
| [W imieniu z](#on-behalf-of) | Aplikacja wywołuje usługę lub interfejsu API sieci web, która z kolei musi wywołać inną usługę lub interfejs API sieci web. Chodzi o to propagację delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. | [Interfejsy API sieci Web](scenario-web-api-call-api-overview.md) |
| [Poświadczenia klienta](#client-credentials) | Umożliwia dostęp do zasobów hostowanych w sieci web przy użyciu tożsamości aplikacji. Często używane do interakcji do serwera, które muszą być uruchamiane w tle, bez natychmiastowego interakcji z użytkownikiem. | [Daemon apps](scenario-daemon-overview.md) |
| [Kod urządzenia](#device-code) | Zezwala użytkownikom na logowanie się do ograniczonych danych wejściowych urządzeń, takich jak telewizor smart TV, urządzenia IoT lub drukarki. | [Desktop/mobile apps](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Uwierzytelnianie zintegrowane Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Umożliwia komputerom aplikacje do domeny lub usługi Azure Active Directory (Azure AD), przyłączone do uzyskania tokenu w trybie dyskretnym (bez żadnej interfejsu użytkownika interakcji ze strony użytkownika).| [Desktop/mobile apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nazwa użytkownika/hasło](scenario-desktop-acquire-token.md#username--password) | Umożliwia aplikacji do logowania użytkownika dzięki obsłudze bezpośrednio swoje hasło. Ten przepływ nie jest zalecane. | [Desktop/mobile apps](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interaktywne
Biblioteka MSAL obsługuje możliwość interaktywnie monitować użytkownika o podanie poświadczeń, zaloguj się w celu uzyskania tokenu przy użyciu tych poświadczeń.

![Diagram przepływu interaktywne](media/msal-authentication-flows/interactive.png)

Aby uzyskać więcej informacji na temat przy użyciu platformy MSAL.NET interaktywnie uzyskania tokenów na określonych platformach zobacz:
- [Xamarin dla systemu Android](msal-net-xamarin-android-considerations.md)
- [Xamarin dla systemu iOS](msal-net-xamarin-ios-considerations.md)
- [Platforma uniwersalna systemu Windows](msal-net-uwp-considerations.md)

Aby uzyskać więcej informacji na temat wywołań interakcyjnego w MSAL.js, zobacz [monit o zachowanie w interaktywnych żądań MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Przyznawanie niejawne

Biblioteka MSAL obsługuje [protokołu OAuth 2 niejawne udzielić przepływ](v2-oauth2-implicit-grant-flow.md), który umożliwia aplikacji uzyskiwanie tokenów z platformą Microsoft identity bez przeprowadzania serwerów zaplecza wymianę poświadczeń. Umożliwia to aplikacji, loguje użytkownika, zachować sesję i uzyskiwanie tokenów do innych interfejsów API sieci web, wszystko to w ramach klienta kodu JavaScript.

![Diagram przepływu przyznawanie niejawne](media/msal-authentication-flows/implicit-grant.svg)

Wiele nowoczesnych aplikacji sieci web są tworzone w aplikacji po stronie klienta, jednej strony, napisane przy użyciu języka JavaScript lub SPA framework, takich jak Angular, Vue.js i React.js. Te aplikacje działają w przeglądarce sieci web i mają właściwości uwierzytelniania innego niż tradycyjne po stronie serwera sieci web aplikacji. Platforma tożsamości firmy Microsoft umożliwia aplikacji jednej strony logowania użytkowników i uzyskiwanie tokenów dostępu do usług zaplecza lub interfejsów API, sieci web przy użyciu przepływu przyznawanie niejawne. Niejawny przepływ umożliwia aplikacji w celu uzyskania tokenów Identyfikatora reprezentują uwierzytelnionego użytkownika, a także dostęp tokenów potrzebnych do wywoływania interfejsów API chronionych.

Ten przebieg uwierzytelniania nie obejmuje scenariusze aplikacji, korzystających z platform JavaScript dla wielu platform, takich jak elektronów i React-Native, ponieważ wymagają one więcej możliwości interakcji z natywnych platform.

## <a name="authorization-code"></a>Kod autoryzacji
Biblioteka MSAL obsługuje [przyznawania kodu autoryzacji protokołu OAuth 2](v2-oauth2-auth-code-flow.md). Tym przydziałem może służyć w aplikacjach, które są zainstalowane na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci web. Pozwala na dodawanie logowania i dostępu do interfejsu API do aplikacji mobilnych i klasycznych. 

Gdy użytkownicy logują się do aplikacji sieci web (witryn sieci Web), aplikacji sieci web otrzyma kod autoryzacji.  Kod autoryzacji jest skorzystać w celu uzyskania tokenu służącego do wywoływania interfejsów API sieci web. W aplikacji sieci web ASP.NET i ASP.NET Core, jedynym celem `AcquireTokenByAuthorizationCode` jest, aby dodać token do pamięci podręcznej tokenu. Tokenu można następnie używane przez aplikację (zwykle w kontrolerów, która po prostu uzyskać token dla interfejsu API przy użyciu `AcquireTokenSilent`).

![Diagram przedstawiający przepływ kodu autoryzacji](media/msal-authentication-flows/authorization-code.png)

Na powyższym diagramie aplikacji:

1. Żąda kod autoryzacji, który jest zrealizowany dla tokenu dostępu.
2. Używa tokenu dostępu do wywołania interfejsu API sieci web.

### <a name="considerations"></a>Zagadnienia do rozważenia
- Aby zrealizować token, można użyć tylko raz za pomocą kodu autoryzacji. Nie należy próbować uzyskać token wiele razy przy użyciu tego samego kodu autoryzacji (jest jawnie zabronione przez specyfikację standardowy protokół). Jeśli zrealizowaniu kod kilka razy celowo lub nie masz pewności, że platforma również zrobi to za Ciebie, zostanie wyświetlony następujący błąd: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Jeśli piszesz aplikację ASP.NET lub ASP.NET Core to może się zdarzyć, jeśli nie poinformować szablon została już wykorzystana kod autoryzacji. W tym celu należy wywołać `context.HandleCodeRedemption()` metody `AuthorizationCodeReceived` programu obsługi zdarzeń.

- Unikaj udostępniania tokenu dostępu za pomocą platformy ASP.NET, które mogą uniemożliwić przyrostowe zgody wykonywane poprawnie. Aby uzyskać więcej informacji, zobacz [wystawiać #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>W imieniu z

Biblioteka MSAL obsługuje [przepływ uwierzytelniania w imieniu z protokołu OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Ten przepływ jest używany, gdy aplikacja wywołuje usługę lub interfejsu API sieci web, która z kolei musi wywołać inną usługę lub interfejs API sieci web. Chodzi o to propagację delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. Usługi warstwy środkowej na wysyłanie żądań uwierzytelnionych usługi transmisji musi ona secure token dostępu z platformą Microsoft identity w imieniu użytkownika.

![Diagram przepływu o imieniu z](media/msal-authentication-flows/on-behalf-of.png)

Na powyższym diagramie:

1. Aplikacja uzyskuje token dostępu dla interfejsu API sieci web.
2. Klient (sieci web, aplikacji mobilnej, klasycznych lub jednostronicowych) wywołuje chroniony internetowy interfejs API, dodając token dostępu jako token elementu nośnego w nagłówku uwierzytelniania żądania HTTP. Interfejs API sieci web uwierzytelnia użytkownika.
3. Gdy klient wywołuje interfejs API sieci web, internetowy interfejs API żąda inny token w imieniu z użytkownika.  
4. Chroniony internetowy interfejs API używa tego tokenu do wywoływania podrzędnego internetowego interfejsu API w imieniu z użytkownika.  Interfejs API sieci web, można także nowszych żądania tokenów dla innych interfejsów API, podrzędne (ale nadal w imieniu tego samego użytkownika).

## <a name="client-credentials"></a>Poświadczenia klienta

Biblioteka MSAL obsługuje [przepływ poświadczeń klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Ten przepływ umożliwia dostęp do zasobów hostowanych w sieci web przy użyciu tożsamości aplikacji. Ten rodzaj przyznanie najczęściej jest używana dla interakcji do serwera, które muszą być uruchamiane w tle, bez natychmiastowego interakcji z użytkownikiem. Aplikacje tego typu są często określane jako demonów lub konta usługi. 

Przyznawanie poświadczeń klienta zezwala przepływu usługi sieci web (klientowi poufnemu) na używanie jej własnych poświadczeń, zamiast personifikacji użytkownika, do uwierzytelniania podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługą sieci web warstwy środkowej, usługą demona lub witryny sieci Web. Wyższy poziom gwarancji Microsoft platformy tożsamości umożliwia także wywoływania usługi do użycia certyfikatu (zamiast wspólny klucz tajny) jako poświadczenie.

> [!NOTE]
> Przepływ poufne klienta nie jest dostępny na platformach mobilnych (platformy UWP, Xamarin.iOS i Xamarin.Android), ponieważ te obsługują tylko aplikacje klienckie publicznych. Aplikacje klienckie publiczny nie wie, jak potwierdzenia tożsamości aplikacji do dostawcy tożsamości. Bezpieczne połączenie może odbyć się na aplikację sieci web lub interfejsu API sieci web ponownie kończy się poprzez wdrożenie certyfikatu.

Platformy MSAL.NET obsługuje dwa typy poświadczeń klienta. Te poświadczenia klienta muszą być zarejestrowane przy użyciu usługi Azure AD. Poświadczenia są przekazywane w konstruktorów aplikacji zawierających poufne dane klienta w kodzie.

### <a name="application-secrets"></a>Wpisy tajne aplikacji 

![Diagram przedstawiający poufne klienta przy użyciu hasła](media/msal-authentication-flows/confidential-client-password.png)

Na powyższym diagramie aplikacji:

1. Uzyskuje token przy użyciu poświadczeń hasła lub hasła aplikacji.
2. Używa tokenu do wysyłania żądań zasobu.

### <a name="certificates"></a>Certyfikaty 

![Diagram przedstawiający poufne klienta przy użyciu certyfikatu](media/msal-authentication-flows/confidential-client-certificate.png)

Na powyższym diagramie aplikacji:

1. Uzyskuje token przy użyciu poświadczeń certyfikatu.
2. Używa tokenu do wysyłania żądań zasobu.

Te poświadczenia klienta muszą być:
- Zarejestrowane w usłudze Azure AD.
- Przekazana w konstrukcji aplikacji zawierających poufne dane klienta w kodzie.


## <a name="device-code"></a>Kod urządzenia
Biblioteka MSAL obsługuje [przepływ kodu urządzenia protokołu OAuth 2](v2-oauth2-device-code.md), co pozwala użytkownikom na logowanie do urządzenia ograniczonych danych wejściowych, takich jak telewizor smart TV, urządzenia IoT lub drukarki. Uwierzytelnianie interakcyjne z usługą Azure AD wymaga przeglądarki sieci web. Przepływ kodu urządzeń umożliwia użytkownikowi używanie innego urządzenia (na przykład innego komputera lub telefon komórkowy) do logowania interakcyjnego, gdy urządzenia i systemy operacyjne nie zapewniają przeglądarki sieci web.

Za pomocą przepływu kodu urządzenia, aplikacji uzyskuje tokenów przejdziesz do dwuetapowego procesu, szczególnie przeznaczonych dla tych urządzeń lub systemów operacyjnych. Przykłady takich aplikacji obejmują uruchomione na urządzeniach IoT lub narzędzia wiersza polecenia (CLI). 

![Diagram przepływu kodu urządzenia](media/msal-authentication-flows/device-code.png)

Na powyższym diagramie:

1. Zawsze, gdy wymagane jest uwierzytelnianie użytkownika, aplikacja zawiera kod i prosi użytkownika o Użyj innego urządzenia (np. smartfony podłączonej do Internetu), aby przejść do adresu URL (na przykład https://microsoft.com/devicelogin). Użytkownik jest następnie zostanie wyświetlony monit o podanie kodu i przechodzi przez środowisko normalnego uwierzytelniania, w tym monitów o wyrażenie zgody i uwierzytelniania wieloskładnikowego, jeśli to konieczne.

2. Po pomyślnym uwierzytelnieniu czyli aplikację wiersza polecenia odbiera wymagane tokenów za pośrednictwem kanału zwrotnego i używa ich do wykonywania wywołań interfejsu API sieci web, których potrzebuje.

### <a name="constraints"></a>Ograniczenia

- Przepływ kodu urządzenia jest dostępna tylko na aplikacje klienckie publicznych.
- Urząd, przekazywane w przypadku tworzenia aplikacji klienckiej publiczny musi być jedną z następujących czynności:
  - Gośćmi (w postaci `https://login.microsoftonline.com/{tenant}/` gdzie `{tenant}` jest albo identyfikator GUID reprezentujący identyfikator dzierżawy lub domeny skojarzone z dzierżawcą).
  - dla każdego roboczych i kont służbowych (`https://login.microsoftonline.com/organizations/`).
- Osobistych kont Microsoft nie są jeszcze obsługiwane przez punkt końcowy usługi Azure AD w wersji 2.0 (nie można użyć `/common` lub `/consumers` dzierżaw).

## <a name="integrated-windows-authentication"></a>Uwierzytelnianie zintegrowane Windows
Biblioteka MSAL obsługuje zintegrowane Windows Authentication (Zintegrowane) dla pulpitu lub aplikacji uruchamianych na przyłączonych do domeny lub usługi Azure AD dla urządzeń przenośnych przyłączony komputer Windows. Za pomocą IWA, te aplikacje mogą uzyskiwać token w trybie dyskretnym (bez żadnej interfejsu użytkownika interakcji ze strony użytkownika). 

![Diagram uwierzytelniania zintegrowanego Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Na powyższym diagramie aplikacji:

1. Uzyskuje token przy użyciu zintegrowanego uwierzytelniania Windows.
2. Używa tokenu do wysyłania żądań zasobu.

### <a name="constraints"></a>Ograniczenia

IWA obsługuje użytkowników federacyjnych, co oznacza użytkowników utworzone w usłudze Active Directory i wspierana przez usługę Azure AD. Użytkownicy, utworzone bezpośrednio w usłudze Azure AD bez usługi Active Directory kopii (zarządzanych użytkowników) nie mogą używać ten przebieg uwierzytelniania. To ograniczenie nie ma wpływu na [nazwy użytkownika/hasła usługi flow](#usernamepassword).

IWA jest dla aplikacji napisanych dla platformy .NET Framework, .NET Core i platformy uniwersalnej Windows.

IWA nie obejście usługi Multi-Factor authentication. Jeśli skonfigurowano uwierzytelnianie wieloskładnikowe, IWA może się nie powieść Jeśli wezwanie do uwierzytelnienia Multi-Factor Authentication jest wymagana. Uwierzytelnianie wieloskładnikowe wymaga interakcji z użytkownikiem.

Nie można kontrolować, gdy dostawca tożsamości żąda uwierzytelniania dwuskładnikowego do wykonania. Jest administratorem dzierżawy. Zazwyczaj uwierzytelnianie dwuskładnikowe jest wymagane po zalogowaniu w innym kraju, jeśli nie masz połączenia za pośrednictwem sieci VPN do sieci firmowej i czasami nawet jeśli masz połączenie za pośrednictwem sieci VPN. Usługa Azure AD używa sztucznej Inteligencji do stale Dowiedz się, jeśli wymagane jest uwierzytelnianie dwuskładnikowe. Jeśli IWA zakończy się niepowodzeniem, użytkownik powinien wrócić do monitowania użytkownika (https://aka.ms/msal-net-interactive).

Urząd, przekazywane w przypadku tworzenia aplikacji klienckiej publiczny musi być jedną z następujących czynności:
- Gośćmi (w postaci `https://login.microsoftonline.com/{tenant}/` gdzie `tenant` jest albo identyfikator guid reprezentujący identyfikator dzierżawy lub domeny skojarzone z dzierżawcą).
- dla każdego roboczych i kont służbowych (`https://login.microsoftonline.com/organizations/`). Nie są obsługiwane osobistych kont Microsoft (nie można użyć `/common` lub `/consumers` dzierżaw).

Ponieważ IWA dyskretnej przepływu, jedną z następujących muszą być spełnione:
- użytkownik aplikacji musi wcześniej wyrażono zgodę na używanie aplikacji. 
- Administrator dzierżawy musi wcześniej wyrażono zgodę na wszystkich użytkowników w dzierżawie korzystanie z aplikacji.

Oznacza to, że jest spełniony jeden z następujących czynności:
- Jako deweloper wybrano **Grant** w witrynie Azure portal dla siebie.
- Administrator dzierżawy została wybrana **Grant/revoke zgody administratora {domenie dzierżawy}** w **uprawnienia do interfejsu API** kartę rejestracji aplikacji (zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci web ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Zostały podane użytkownikom na wyrażanie zgody na aplikację (zobacz [żądanie zgody użytkownika](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Zostały podane sposób administrator dzierżawy wyrazić zgodę dla aplikacji (zobacz [zgody administratora](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Przepływ IWA jest włączona dla klasycznych na platformie .NET, .NET Core i platformy Universal Windows apps. Na platformie .NET Core tylko przeciążenie nazwa użytkownika jest dostępna. Platforma .NET Core zapytać nazwy użytkownika w systemie operacyjnym.
  
Aby uzyskać więcej informacji na temat zgody, zobacz [v2.0 uprawnienia i zgody](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nazwa użytkownika/hasło 
Biblioteka MSAL obsługuje [przyznanie poświadczeń hasła właściciela zasobów OAuth 2](v2-oauth-ropc.md), co umożliwia aplikacji do logowania użytkownika dzięki obsłudze bezpośrednio swoje hasło. W aplikacji pulpitu można użyć przepływu nazwy użytkownika i hasła można uzyskać tokenu w trybie dyskretnym. Brak interfejsu użytkownika jest wymagana w przypadku korzystania z aplikacji.

![Diagram przepływu nazwy użytkownika i hasła](media/msal-authentication-flows/username-password.png)

Na powyższym diagramie aplikacji:

1. Uzyskuje token, wysyłając nazwę użytkownika i hasło do dostawcy tożsamości.
2. Wywołuje interfejs API sieci web za pomocą tokenu.

> [!WARNING]
> Ten przepływ nie jest zalecane. Wymaga wysokiego stopnia narażenia zaufania i użytkownika.  Ten przepływ należy używać tylko w przypadku, gdy nie można użyć innych, bardziej bezpiecznymi przepływów. Aby uzyskać więcej informacji, zobacz [co to jest rozwiązanie rosnącą problemu związanego z haseł?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Preferowany przepływ w celu uzyskania tokenu w trybie dyskretnym na komputerach przyłączonych do domeny Windows jest [zintegrowane uwierzytelnianie Windows](#integrated-windows-authentication). W przeciwnym razie możesz również użyć [przepływ kodu urządzenia](#device-code).

Mimo że jest to przydatne w niektórych przypadkach (scenariuszy DevOps), jeśli chcesz używać nazwy użytkownika i hasła w interakcyjnych scenariuszy, w którym udostępniać własnego interfejsu użytkownika, spróbuj się uniknąć tego błędu. Przy użyciu nazwy użytkownika/hasła:
- Użytkownicy, którzy muszą przeprowadzić uwierzytelnianie wieloskładnikowe nie będzie mógł zalogować (zgodnie z nie zachodzi żadna interakcja).
- Użytkownicy nie będą mogli logowanie jednokrotne.

### <a name="constraints"></a>Ograniczenia

Z wyjątkiem [ograniczenia zintegrowane uwierzytelnianie Windows](#integrated-windows-authentication), także obowiązują następujące ograniczenia:

- Przepływ nazwy użytkownika/hasła nie jest zgodny z dostępu warunkowego i uwierzytelniania wieloskładnikowego. W rezultacie jeśli aplikacja działa w dzierżawie usługi Azure AD, w którym administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie możesz użyć tego przepływu. Wiele organizacji to zrobić.
- Działa tylko w przypadku kont służbowych (a nie konta Microsoft).
- Przepływ jest dostępne w klasycznych na platformie .NET i .NET Core, ale nie na uniwersalnym platformy Windows.

### <a name="azure-ad-b2c-specifics"></a>Usługa Azure AD B2C szczegółowych informacji.

Aby uzyskać więcej informacji na temat korzystania z platformy MSAL.NET i Azure AD B2C, zobacz [ROPC korzystanie z usługi Azure AD B2C (platformy MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
