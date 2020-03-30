---
title: Aplikacje interfejsu API sieci Web w usłudze Azure Active Directory
description: W tym artykule opisano, jakie są aplikacje interfejsu API sieci web i podstawy dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154427"
---
# <a name="web-api"></a>Interfejs API sieci Web

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje interfejsu API sieci Web to aplikacje sieci Web, które muszą uzyskać zasoby z internetowego interfejsu API. W tym scenariuszu istnieją dwa typy tożsamości, których aplikacja sieci web może używać do uwierzytelniania i wywoływania internetowego interfejsu API:

- **Tożsamość aplikacji** — w tym scenariuszu użyto poświadczenia klienta OAuth 2.0 do uwierzytelniania jako aplikacji i dostępu do internetowego interfejsu API. Podczas korzystania z tożsamości aplikacji, interfejs API sieci web można tylko wykryć, że aplikacja sieci web wywołuje go, jak interfejs API sieci web nie odbiera żadnych informacji o użytkowniku. Jeśli aplikacja odbiera informacje o użytkowniku, zostanie wysłana za pośrednictwem protokołu aplikacji i nie jest podpisana przez usługę Azure AD. Internetowy interfejs API ufa, że aplikacja sieci web uwierzytelniła użytkownika. Z tego powodu ten wzorzec jest nazywany zaufanym podsystemem.
- **Tożsamości użytkownika delegowanego** — ten scenariusz można wykonać na dwa sposoby: OpenID Connect i OAuth 2.0 kod autoryzacji udzielić z poufnego klienta. Aplikacja sieci web uzyskuje token dostępu dla użytkownika, który udowadnia w interfejsie API sieci web, że użytkownik pomyślnie uwierzytelniony do aplikacji sieci web i że aplikacja sieci web mogła uzyskać delegowaną tożsamość użytkownika, aby wywołać internetowy interfejs API. Ten token dostępu jest wysyłany w żądaniu do internetowego interfejsu API, który autoryzuje użytkownika i zwraca żądany zasób.

Zarówno tożsamości aplikacji i delegowanych typów tożsamości użytkownika są omówione w przepływie poniżej. Kluczową różnicą między nimi jest to, że tożsamość użytkownika delegowanego musi najpierw uzyskać kod autoryzacji, zanim użytkownik może się zalogować i uzyskać dostęp do internetowego interfejsu API.

## <a name="diagram"></a>Diagram

![Diagram interfejsu API aplikacji sieci Web do sieci Web](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Tożsamość aplikacji z dotacje poświadczeń klienta OAuth 2.0

1. Użytkownik jest zalogowany do usługi Azure AD w aplikacji sieci web (zobacz **sekcję aplikacje sieci Web,** aby uzyskać więcej informacji).
1. Aplikacja sieci web musi uzyskać token dostępu, dzięki czemu można uwierzytelnić do internetowego interfejsu API i pobrać żądany zasób. Powoduje, że żądanie do punktu końcowego tokenu usługi Azure AD, podając poświadczenia, identyfikator aplikacji i identyfikator URI identyfikatora aplikacji interfejsu internetowego.
1. Usługa Azure AD uwierzytelnia aplikację i zwraca token dostępu JWT, który jest używany do wywoływania internetowego interfejsu API.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci web używa zwróconego tokenu dostępu JWT do dodania ciągu JWT z oznaczeniem "Nośnik" w nagłówku autoryzacji żądania do internetowego interfejsu API. Internetowy interfejs API następnie sprawdza poprawność tokenu JWT, a jeśli sprawdzanie poprawności zakończy się pomyślnie, zwraca żądany zasób.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegowana tożsamość użytkownika za pomocą OpenID Connect

1. Użytkownik jest zalogowany do aplikacji sieci web przy użyciu usługi Azure AD (zobacz przeglądarkę sieci Web do aplikacji sieci Web powyżej). Jeśli użytkownik aplikacji sieci web nie wyraził jeszcze zgody na zezwolenie aplikacji sieci web na wywołanie internetowego interfejsu API w jej imieniu, użytkownik będzie musiał wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia, a jeśli którekolwiek z nich są uprawnieniami na poziomie administratora, zwykły użytkownik w katalogu nie będzie mógł wyrazić zgody. Ten proces zgody dotyczy tylko aplikacji wielodostępnych, a nie aplikacji pojedynczej dzierżawy, ponieważ aplikacja będzie już mieć niezbędne uprawnienia. Gdy użytkownik się zalogował, aplikacja sieci web odebrała token identyfikatora z informacjami o użytkowniku, a także kodem autoryzacji.
1. Przy użyciu kodu autoryzacji wystawionego przez usługę Azure AD aplikacja sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, które zawiera kod autoryzacji, szczegóły dotyczące aplikacji klienckiej (identyfikator aplikacji i przekierowania identyfikatora URI) oraz żądany zasób (identyfikator aplikacji identyfikator URI dla internetowego interfejsu API).
1. Kod autoryzacji i informacje o aplikacji sieci web i interfejsie API sieci Web są sprawdzane przez usługę Azure AD. Po pomyślnym sprawdowaniu poprawności usługa Azure AD zwraca dwa tokeny: token dostępu JWT i token odświeżania JWT.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci web używa zwróconego tokenu dostępu JWT do dodania ciągu JWT z oznaczeniem "Nośnik" w nagłówku autoryzacji żądania do internetowego interfejsu API. Internetowy interfejs API następnie sprawdza poprawność tokenu JWT, a jeśli sprawdzanie poprawności zakończy się pomyślnie, zwraca żądany zasób.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegowana tożsamość użytkownika z udzieleniem kodu autoryzacji OAuth 2.0

1. Użytkownik jest już zalogowany do aplikacji sieci web, której mechanizm uwierzytelniania jest niezależny od usługi Azure AD.
1. Aplikacja sieci web wymaga kodu autoryzacji, aby uzyskać token dostępu, więc wystawia żądanie za pośrednictwem przeglądarki do punktu końcowego autoryzacji usługi Azure AD, podając identyfikator aplikacji i przekieruj identyfikator URI dla aplikacji sieci web po pomyślnym uwierzytelnieniu. Użytkownik loguje się do usługi Azure AD.
1. Jeśli użytkownik aplikacji sieci web nie wyraził jeszcze zgody na zezwolenie aplikacji sieci web na wywołanie internetowego interfejsu API w jej imieniu, użytkownik będzie musiał wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia, a jeśli którekolwiek z nich są uprawnieniami na poziomie administratora, zwykły użytkownik w katalogu nie będzie mógł wyrazić zgody. Ta zgoda dotyczy aplikacji jedno- i wielodostępnych. W przypadku pojedynczej dzierżawy administrator może wykonać zgodę administratora na wyrażenie zgody w imieniu swoich użytkowników. Można to zrobić `Grant Permissions` za pomocą przycisku w [witrynie Azure portal](https://portal.azure.com). 
1. Po zezwoleniu użytkownik otrzyma kod autoryzacji, który musi uzyskać token dostępu.
1. Przy użyciu kodu autoryzacji wystawionego przez usługę Azure AD aplikacja sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, które zawiera kod autoryzacji, szczegóły dotyczące aplikacji klienckiej (identyfikator aplikacji i przekierowania identyfikatora URI) oraz żądany zasób (identyfikator aplikacji identyfikator URI dla internetowego interfejsu API).
1. Kod autoryzacji i informacje o aplikacji sieci web i interfejsie API sieci Web są sprawdzane przez usługę Azure AD. Po pomyślnym sprawdowaniu poprawności usługa Azure AD zwraca dwa tokeny: token dostępu JWT i token odświeżania JWT.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci web używa zwróconego tokenu dostępu JWT do dodania ciągu JWT z oznaczeniem "Nośnik" w nagłówku autoryzacji żądania do internetowego interfejsu API. Internetowy interfejs API następnie sprawdza poprawność tokenu JWT, a jeśli sprawdzanie poprawności zakończy się pomyślnie, zwraca żądany zasób.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji sieci Web do scenariuszy interfejsu API sieci Web. I, sprawdzaj często - nowe próbki są dodawane często. Aplikacja sieci Web [do interfejsu API sieci Web](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację w punkcie końcowym usługi Azure AD w wersji 1.0, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Pojedyncza dzierżawa — zarówno w przypadku tożsamości aplikacji, jak i przypadków tożsamości delegowanego użytkownika aplikacja sieci web i internetowy interfejs API muszą być zarejestrowane w tym samym katalogu w usłudze Azure AD. Internetowy interfejs API można skonfigurować w taki sposób, aby udostępniał zestaw uprawnień, które są używane do ograniczania dostępu aplikacji sieci web do jej zasobów. Jeśli używany jest delegowany typ tożsamości użytkownika, aplikacja sieci web musi wybrać żądane uprawnienia z menu rozwijanego **Uprawnienia do innych aplikacji** w witrynie Azure portal. Ten krok nie jest wymagany, jeśli używany jest typ tożsamości aplikacji.
* Wielodostępne — najpierw aplikacja sieci web jest skonfigurowana do wskazywania uprawnień, które wymaga, aby były funkcjonalne. Ta lista wymaganych uprawnień jest wyświetlana w oknie dialogowym, gdy użytkownik lub administrator w katalogu docelowym wyraża zgodę na aplikację, która udostępnia ją swojej organizacji. Niektóre aplikacje wymagają tylko uprawnień na poziomie użytkownika, na które może wyrazić zgodę każdy użytkownik w organizacji. Inne aplikacje wymagają uprawnień na poziomie administratora, na które użytkownik w organizacji nie może wyrazić zgody. Tylko administrator katalogu może wyrazić zgodę na aplikacje, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyrazi na to zgodę, aplikacja sieci web i internetowy interfejs API są rejestrowane w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacja sieci web używa swojego kodu autoryzacji, aby uzyskać token dostępu JWT, odbiera również token odświeżania JWT. Po wygaśnięciu tokenu dostępu token odświeżania może służyć do ponownego uwierzytelniania użytkownika bez konieczności ponownego logowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, co powoduje nowy token dostępu i odświeżanie tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [typach i scenariuszach aplikacji](app-types.md)
- Dowiedz się więcej o [podstawach uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
