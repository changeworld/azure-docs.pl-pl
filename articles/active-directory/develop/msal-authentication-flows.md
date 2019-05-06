---
title: Aplikacje klienckie (Microsoft Authentication Library) | Azure
description: Dowiedz się więcej o publicznych klienta i poufne klienta aplikacji w programie Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 096aa5e5ce2f33467457cef22220f338ae49b708
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139096"
---
# <a name="authentication-flows"></a>Przepływy uwierzytelniania

W tym artykule opisano przepływy uwierzytelniania inny, pod warunkiem Microsoft Authentication Library (MSAL).  Te przepływy może służyć w wielu różnych zastosowaniach.

| Ruch | Opis | Używane w|  
| ---- | ----------- | ------- | 
| [Przyznawanie niejawne](#implicit-grant) | Umożliwia aplikacji uzyskiwanie tokenów bez przeprowadzania wymianę poświadczeń serwera wewnętrznej bazy danych. Umożliwia to aplikacji, loguje użytkownika, zachować sesję i uzyskiwanie tokenów do innych interfejsów API sieci web w klienta kodu JavaScript.| [Aplikacji jednostronicowej (SPA)](scenario-spa-overview.md) |
| [Kod autoryzacji](#authorization-code) | Używane w aplikacjach, które są zainstalowane na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci web. Pozwala na dodawanie, zaloguj się i interfejs API dostępu do aplikacji mobilnych i klasycznych. | [Aplikacje komputerowe](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md), [aplikacje sieci Web](scenario-web-app-call-api-overview.md) | 
| [W imieniu z](#on-behalf-of) | Aplikacja wywołuje usługę/internetowego interfejsu API, które z kolei są potrzebne do wywoływania innej usługi/interfejsu API sieci web. Chodzi o to propagację delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. | [Interfejsy API sieci Web](scenario-web-api-call-api-overview.md) |
| [Poświadczenia klienta](#client-credentials) | Umożliwia dostęp do zasobów hostowanych w sieci web przy użyciu tożsamości aplikacji. Często używane do interakcji do serwera, które muszą być uruchamiane w tle, bez natychmiastowego interakcji z użytkownikiem. | [Daemon apps](scenario-daemon-overview.md) |
| [Kod urządzenia](#device-code) | Zezwala użytkownikom na logowanie się do ograniczonych danych wejściowych urządzeń, takich jak telewizor smart TV, urządzenia IoT lub drukarki. | [Desktop/Mobile apps](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Uwierzytelnianie zintegrowane Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Zezwala aplikacji na domeny lub usługi Azure AD przyłączona do komputerów, które można uzyskać tokenu w trybie dyskretnym (bez żadnej interfejsu użytkownika interakcji ze strony użytkownika).| [Desktop/Mobile apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nazwa użytkownika/hasło](scenario-desktop-acquire-token.md#username--password) | Umożliwia aplikacji do logowania użytkownika dzięki obsłudze bezpośrednio swoje hasło. Ten przepływ nie jest zalecane. | [Desktop/mobile apps](scenario-desktop-acquire-token.md#username--password) | 


## <a name="implicit-grant"></a>Niejawne przyznanie

Biblioteka MSAL obsługuje [protokołu OAuth 2 niejawne udzielić przepływu](v2-oauth2-implicit-grant-flow.md), który umożliwia aplikacji uzyskiwanie tokenów z platformą Microsoft identity bez przeprowadzania serwera wewnętrznej bazy danych programu exchange poświadczeń. Umożliwia to aplikacji, loguje użytkownika, zachować sesję i uzyskiwanie tokenów do innych interfejsów API sieci web w klienta kodu JavaScript.

![Przyznawanie niejawne przepływu](media/msal-authentication-flows/implicit-grant.svg)

Wiele nowoczesnych aplikacji sieci web są tworzone w aplikacji jednostronicowej po stronie klienta, napisane przy użyciu języka JavaScript lub SPA framework, takich jak Angular, Vue.js i React.js. Te aplikacje działają w przeglądarce sieci web i mają właściwości uwierzytelniania innego niż tradycyjne po stronie serwera sieci web aplikacji. Platforma tożsamości firmy Microsoft umożliwia aplikacji jednej strony logowania użytkowników i uzyskiwanie tokenów dostępu do usług zaplecza lub interfejsów API przy użyciu usługi flow przyznawanie niejawne sieci web. Niejawny przepływ umożliwia aplikacji w celu uzyskania tokenów Identyfikatora reprezentują uwierzytelnionego użytkownika, a także dostęp do tokenów potrzebnych do wywoływania interfejsów API chronionego.

Ten przebieg uwierzytelniania nie obejmuje scenariusze aplikacji przy użyciu platform JavaScript dla wielu platform, takich jak elektronów i React-Native, ponieważ wymagają one więcej możliwości interakcji z natywnych platform.

## <a name="authorization-code"></a>Kod autoryzacji
Biblioteka MSAL obsługuje [przyznawania kodu autoryzacji protokołu OAuth 2](v2-oauth2-auth-code-flow.md), których można używać w aplikacjach, które są zainstalowane na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci web. Pozwala na dodawanie, zaloguj się i interfejs API dostępu do aplikacji mobilnych i klasycznych. 

Gdy użytkownicy logują się do aplikacji sieci web (witryny sieci web), aplikacji sieci web otrzyma kod autoryzacji.  Kod autoryzacji jest skorzystać w celu uzyskania tokenu służącego do wywoływania interfejsów API sieci web. W programie ASP.NET: / ASP.NET core z aplikacji sieci web, jedynym celem `AcquireTokenByAuthorizationCode` jest aby dodać token do pamięci podręcznej tokenu, aby następnie może być używane przez aplikację (zwykle w kontrolerach), która po prostu uzyskać token dla interfejsu API przy użyciu `AcquireTokenSilent`.

![Przepływ kodu autoryzacji](media/msal-authentication-flows/authorization-code.png)

### <a name="considerations"></a>Zagadnienia do rozważenia
- Kod autoryzacji jest można używać tylko raz zrealizować token. Nie należy próbować uzyskać token wiele razy przy użyciu tego samego kodu autoryzacji (jest jawnie zabronione przez specyfikację standardowy protokół). Jeśli możesz zrealizować kod kilka razy celowo lub nie masz pewności, że platforma również zrobi to za Ciebie, wystąpi błąd: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Jeśli piszesz aplikację ASP.NET/ASP.NET Core może się to zdarzyć, jeśli nie informuj ASP.NET/Core framework została już wykorzystana kod autoryzacji. W tym celu należy wywołać `context.HandleCodeRedemption()` metody `AuthorizationCodeReceived` programu obsługi zdarzeń.

- Unikaj udostępniania tokenu dostępu za pomocą platformy ASP.NET, które mogą uniemożliwić przyrostowe zgody wykonywane poprawnie.  Aby uzyskać więcej informacji, zobacz [wystawiać #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>W imieniu z

Biblioteka MSAL obsługuje [przepływ uwierzytelniania w imieniu z protokołu OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Ten przepływ jest używany, gdy aplikacja wywołuje usługę/internetowego interfejsu API, które z kolei są potrzebne do wywoływania innej usługi/interfejsu API sieci web. Chodzi o to propagację delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. Usługi warstwy środkowej na wysyłanie żądań uwierzytelnionych usługi transmisji musi ona secure token dostępu z platformą Microsoft identity w imieniu użytkownika.

![Przepływ „w imieniu”](media/msal-authentication-flows/on-behalf-of.png)

1. Uzyskuje token dostępu interfejsu API sieci Web
2. Klient (sieć Web, mobilnych, aplikacji jednej strony pulpitu,) wywołuje chroniony internetowy interfejs API, dodając token dostępu jako token elementu nośnego w nagłówku uwierzytelniania żądania HTTP. Interfejs API sieci Web uwierzytelnia użytkownika.
3. Kiedy klient wywołuje interfejs API sieci Web, interfejsu API sieci Web żąda inny token w imieniu z użytkownika.  
4. Chronionego interfejsu API sieci Web używa tego tokenu do wywoływania podrzędnego internetowego interfejsu API w imieniu z użytkownika.  Interfejs API sieci Web również później może żądać tokenów dla innych interfejsów API, podrzędne (ale nadal w imieniu tego samego użytkownika).

## <a name="client-credentials"></a>Poświadczenia klienta

Biblioteka MSAL obsługuje [przepływ poświadczeń klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Ten przepływ umożliwia dostęp do zasobów hostowanych w sieci web przy użyciu tożsamości aplikacji. Ten rodzaj przyznanie najczęściej jest używana dla interakcji do serwera, które muszą być uruchamiane w tle, bez natychmiastowego interakcji z użytkownikiem. Aplikacje tego typu są często określane jako demonów lub konta usługi. 

Poświadczenia klienta udzielić zezwolenia przepływ usługi sieci web (poufne klienta), aby używanie jej własnych poświadczeń, zamiast personifikacji użytkownika, do uwierzytelniania podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługi sieci web warstwy środkowej, usługą demona lub witryną sieci web. Wyższy poziom gwarancji Microsoft platformy tożsamości umożliwia także wywoływania usługi do użycia certyfikatu (zamiast wspólny klucz tajny) jako poświadczenie.

> [!NOTE]
> Przepływ poufne klienta nie jest dostępny na platformach mobilnych (platformy UWP, Xamarin.iOS i Xamarin.Android), ponieważ te obsługują tylko aplikacje klienckie publicznych.  Aplikacje klienckie publiczny nie wie, jak potwierdzenia tożsamości aplikacji do dostawcy tożsamości. Bezpieczne połączenie może odbyć się na aplikację sieci web lub internetowy interfejs API zaplecza poprzez wdrożenie certyfikatu.

Platformy MSAL.NET obsługuje trzy typy poświadczeń klienta:

- Wpisy tajne aplikacji <BR>![Poufne klienta przy użyciu hasła](media/msal-authentication-flows/confidential-client-password.png)
- Certyfikaty <BR>![Poufne klienta przy użyciu certyfikatu](media/msal-authentication-flows/confidential-client-certificate.png)
- Potwierdzenia zoptymalizowane klienta<BR>![Poufne klienta z potwierdzeniami](media/msal-authentication-flows/confidential-client-assertions.png)

Te poświadczenia klienta muszą być:

- Zarejestrowane w usłudze Azure AD.
- Przekazana w konstrukcji aplikacji zawierających poufne dane klienta w kodzie.


## <a name="device-code"></a>Kod urządzenia
Biblioteka MSAL obsługuje [przepływ kodu urządzenia protokołu OAuth 2](v2-oauth2-device-code.md), co pozwala użytkownikom na logowanie się do ograniczonych danych wejściowych urządzeń, takich jak telewizor smart TV, urządzenia IoT lub drukarki. Uwierzytelnianie interakcyjne z usługą Azure AD wymaga przeglądarki sieci web. Przepływ kodu urządzeń użytkownicy będą mogli używać innego urządzenia (na przykład innego komputera lub telefon komórkowy) do logowania interakcyjnego gdzie urządzenia i systemy operacyjne nie zapewnia przeglądarki sieci Web.

Za pomocą przepływu kodu urządzenia, aplikacji uzyskuje tokenów przejdziesz do dwuetapowego procesu, szczególnie zaprojektowana z myślą o tych urządzeń/OS. Przykładami takich aplikacji są aplikacje działające na urządzeniach iOT lub narzędzia wiersza polecenia (CLI). 

![Przepływ kodu urządzenia](media/msal-authentication-flows/device-code.png)

1. Zawsze wtedy, gdy wymagane jest uwierzytelnianie użytkownika, aplikacja zawiera kod i prosi użytkownika o za pomocą innego urządzenia (np. smartfony podłączonej do Internetu) przejdź do adresu URL (na przykład http://microsoft.com/devicelogin), gdzie użytkownicy będą monitowani o podanie kodu. Czy to zrobione, strony sieci web przeprowadzi użytkownika przez środowisko normalnego uwierzytelniania, w tym monitów o wyrażenie zgody i uwierzytelniania wieloskładnikowego, jeśli to konieczne.

2. Po pomyślnym uwierzytelnieniu czyli aplikację wiersza polecenia otrzyma wymagane tokenów za pośrednictwem kanału zwrotnego i użyje do wykonywania wywołań interfejsu API sieci web, których potrzebuje.

### <a name="constraints"></a>Ograniczenia

- Przepływ kodu urządzenia jest dostępna tylko na aplikacje klienckie publicznych.
- Organ przekazanej podczas tworzenia aplikacji klienckiej publiczny musi być:
  - gośćmi (w postaci `https://login.microsoftonline.com/{tenant}/` gdzie `{tenant}` jest albo identyfikator GUID reprezentujący identyfikator dzierżawy lub domeny skojarzone z dzierżawcą.
  - ewentualnie wszelkie oraz do kont służbowych (`https://login.microsoftonline.com/organizations/`).
- Osobistych kont Microsoft nie są jeszcze obsługiwane przez punkt końcowy usługi Azure AD w wersji 2.0 (nie można użyć `/common` lub `/consumers` dzierżaw).

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows
Biblioteka MSAL obsługuje zintegrowane Windows Authentication (Zintegrowane) dla pulpitu lub aplikacji uruchamianych na przyłączonych do domeny lub usługi Azure AD dla urządzeń przenośnych przyłączony komputer Windows. Za pomocą IWA, te aplikacje mogą uzyskiwać token w trybie dyskretnym (bez żadnej interfejsu użytkownika interakcji ze strony użytkownika). 

![Zintegrowane uwierzytelnianie systemu Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

### <a name="constraints"></a>Ograniczenia

Obsługuje IWA **Sfederowane** tylko użytkowników.  Użytkownicy utworzone w usłudze Active Directory i wspierana przez usługę Azure Active Directory. Użytkownicy utworzeni bezpośrednio w usłudze Azure AD, bez zapasowego AD (**zarządzane** użytkowników) nie może korzystać z tego przepływu uwierzytelniania. To ograniczenie nie ma wpływu na [nazwy użytkownika/hasła usługi flow](#usernamepassword).

IWA jest dla aplikacji napisanych dla platformy .NET Framework, .NET Core i platformy uniwersalnej Windows.

IWA nie obejście uwierzytelniania Wieloskładnikowego (uwierzytelniania współczynnik multi). Jeśli skonfigurowano uwierzytelnianie wieloskładnikowe, IWA może zakończyć się niepowodzeniem, jeśli żądanie usługi MFA jest wymagana, ponieważ usługa MFA wymaga interakcji z użytkownikiem. To zadanie jest trudne. Zintegrowane nie jest interakcyjny, ale dwuskładnikowego (2FA) wymaga interakcji ze strony użytkownika. Kontroluje, gdy dostawca tożsamości żąda uwierzytelniania 2fa przed uzyskaniem można wykonać, jest administratorem dzierżawy. Z naszej uwagi funkcji 2FA jest wymagana podczas logowania z innego kraju, gdy nie jest połączone za pośrednictwem sieci VPN z siecią firmową, a czasami nawet w przypadku połączenia za pośrednictwem sieci VPN. Nie będziesz już deterministyczne zestaw reguł, usługi Azure Active Directory używa sztucznej Inteligencji, aby stale Dowiedz się, czy konieczne jest 2FA. Należy powrót do monitowania użytkownika (https://aka.ms/msal-net-interactive) Jeśli IWA zakończy się niepowodzeniem.

Organ przekazanej podczas tworzenia aplikacji klienckiej publiczny musi być:
- gośćmi (w postaci `https://login.microsoftonline.com/{tenant}/` gdzie `tenant` jest albo identyfikator guid reprezentujący identyfikator dzierżawy lub domeny skojarzone z dzierżawcą.
- dla każdego roboczych i kont służbowych (`https://login.microsoftonline.com/organizations/`). Nie są obsługiwane osobistych kont Microsoft (nie można użyć `/common` lub `/consumers` dzierżaw).

Ponieważ IWA dyskretnej przepływu:
- użytkownik aplikacji musi wcześniej wyrażono zgodę na używanie aplikacji. 
- lub Administrator dzierżawy musi wcześniej wyrażono zgodę na wszystkich użytkowników w dzierżawie korzystanie z aplikacji.
- Oznacza to, że:
    - albo jako deweloper naciśniętych **Grant** przycisku w witrynie Azure portal dla siebie 
    - lub administratora dzierżawy nacisnął **Grant/revoke zgody administratora {domenie dzierżawy}** znajdujący się w **uprawnienia do interfejsu API** kartę rejestracji aplikacji (zobacz [Dodaj uprawnienia do dostęp do interfejsów API sieci web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - lub podano sposób użytkownikom na wyrażanie zgody aplikacji (zobacz [żądanie zgody użytkownika](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - lub podano sposób administrator dzierżawy wyrazić zgodę dla aplikacji (zobacz [zgody administratora](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

Przepływ IWA jest włączona dla klasycznych na platformie .NET, .NET Core i platformy Universal Windows apps. Na platformie .NET Core tylko przeciążenie nazwa użytkownika jest dostępna, ponieważ platformy .NET Core nie może zapytać nazwa użytkownika do systemu operacyjnego.
  
Aby uzyskać więcej informacji na temat zgody, zobacz [v2.0 uprawnienia i zgody](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nazwa użytkownika/hasło 
Biblioteka MSAL obsługuje [przyznanie poświadczeń hasła właściciela zasobów OAuth 2](v2-oauth-ropc.md), co umożliwia aplikacji do logowania użytkownika dzięki obsłudze bezpośrednio swoje hasło. W aplikacji pulpitu można użyć przepływu nazwy użytkownika i hasła można uzyskać tokenu w trybie dyskretnym. Brak interfejsu użytkownika jest wymagana w przypadku korzystania z aplikacji.

![Nazwy użytkownika/hasła usługi flow](media/msal-authentication-flows/username-password.png)

> [!WARNING]
> Ten przepływ jest **niezalecane** ponieważ wymaga wysokiego stopnia narażenia zaufania i użytkownika.  Ten przepływ należy używać tylko w przypadku, gdy nie można użyć innych, bardziej bezpiecznymi przepływów. Aby uzyskać więcej informacji na temat tego problemu, zobacz [w tym artykule](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Preferowany przepływ w celu uzyskania tokenu w trybie dyskretnym na komputerach przyłączonych do domeny Windows jest [zintegrowane uwierzytelnianie Windows](#integrated-windows-authentication). W przeciwnym razie możesz również użyć [przepływ kodu urządzenia](#device-code)

Mimo że jest to przydatne w niektórych przypadkach (scenariuszy DevOps), jeśli chcesz używać nazwy użytkownika i hasła w interakcyjnych scenariuszy, w którym udostępniać własnego interfejsu użytkownika, należy tak naprawdę rozważać jak przenieść poza. Za pomocą nazwy użytkownika/hasła, możesz są zapewniając telefoniczny kilka rzeczy:
- podstawowe dzierżaw nowoczesnych tożsamości: hasło pobiera połowy, odtworzyć. Ponieważ mamy tę koncepcję udziału klucz tajny, który może zostać przechwycona.
Jest to niezgodne z bez hasła.
- Użytkownicy, którzy muszą przeprowadzić uwierzytelnianie wieloskładnikowe nie będzie mógł zalogować (zgodnie z nie zachodzi żadna interakcja)
- Użytkownicy nie będą mieć możliwość logowania jednokrotnego

### <a name="constraints"></a>Ograniczenia

Z wyjątkiem [ograniczenia zintegrowane uwierzytelnianie Windows](#integrated-windows-authentication), także obowiązują następujące ograniczenia:

- Przepływ nazwy użytkownika/hasła nie jest zgodny z dostępu warunkowego i uwierzytelniania wieloskładnikowego: W rezultacie jeśli aplikacja działa w dzierżawie usługi Azure AD, w którym administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie możesz użyć tego przepływu. Wiele organizacji to zrobić.
- Działa tylko w przypadku pracy i konta służbowe (nie MSA)
- Przepływ jest dostępne w klasycznych na platformie .NET i .NET core, ale nie na uniwersalnym platformy Windows.

### <a name="azure-ad-b2c-specifics"></a>Usługa Azure AD B2C szczegółowych informacji.

Aby uzyskać więcej informacji na temat korzystania z platformy MSAL.NET i Azure AD B2C, przeczytaj [ROPC korzystanie z usługi Azure AD B2C (platformy MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).