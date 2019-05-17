---
title: Aplikacje interfejsu API sieci Web w usłudze Azure Active Directory
description: Opisuje co to są aplikacje interfejsu API sieci web i podstawowe informacje dotyczące protokołu przepływu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 484e6b4c5f0e064254c957b07b8ba15ef98f2634
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545210"
---
# <a name="web-api"></a>Interfejs API sieci Web

Aplikacje interfejsu API sieci Web są aplikacji sieci web, które muszą uzyskać zasoby z internetowego interfejsu API. W tym scenariuszu istnieją dwa typy tożsamości, które aplikacja sieci web można użyć do uwierzytelniania i wywoływanie internetowego interfejsu API:

- **Tożsamość aplikacji** — w tym scenariuszu przyznanie poświadczenia klienta OAuth 2.0 do uwierzytelniania aplikacji i dostępu do interfejsu API sieci web. Podczas korzystania z tożsamości aplikacji sieci web interfejsu API tylko może wykryć, czy wywołanie aplikacji sieci web, internetowego interfejsu API nie otrzymywać żadnych informacji o użytkowniku. Jeśli aplikacja otrzyma informacje o użytkowniku, będą wysyłane za pośrednictwem protokołu aplikacji, a nie jest podpisany przez usługę Azure AD. Internetowy interfejs API zaufania, że aplikacja sieci web uwierzytelnił użytkownika. Z tego powodu ten wzorzec jest nazywany zaufany podsystem.
- **Delegowana tożsamość użytkownika** — w tym scenariuszu można zrobić na dwa sposoby: OpenID Connect i przyznawanie kodu autoryzacji OAuth 2.0 przy użyciu poufne klienta. Aplikacja sieci web uzyskuje token dostępu dla użytkownika, który okazuje się do internetowego interfejsu API, że użytkownik pomyślnie uwierzytelniony do aplikacji sieci web i aplikacji sieci web był w stanie uzyskać tożsamość delegowany użytkownik w celu wywołania interfejsu API sieci web. Ten token dostępu wysyłane żądanie do internetowego interfejsu API, które udzielają użytkownikowi autoryzacji i zwraca żądanego zasobu.

Tożsamość aplikacji i typów tożsamości delegowany użytkownik zostały omówione w usłudze flow poniżej. Klucza różnica między nimi polega na tym, czy tożsamość delegowany użytkownik najpierw muszą uzyskać kod autoryzacji, zanim użytkownik może zarejestrować i uzyskać dostęp do interfejsu API sieci web.

## <a name="diagram"></a>Diagram

![Aplikacja sieci Web do internetowego interfejsu API diagramu](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokołu przepływu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udzielanie tożsamości aplikacji przy użyciu poświadczeń klienta OAuth 2.0

1. Użytkownik jest zalogowany do usługi Azure AD w aplikacji sieci web (zobacz **aplikacje sieci Web** sekcji, aby uzyskać więcej informacji).
1. Aplikacja sieci web musi uzyskiwanie tokenu dostępu, dzięki czemu mogą uwierzytelniania interfejsu API sieci web i pobrać żądanego zasobu. Kieruje żądanie do usługi Azure AD punktu końcowego tokenu, podając poświadczenia, identyfikator aplikacji i identyfikator URI aplikacji internetowego interfejsu API.
1. Usługa Azure AD uwierzytelnia aplikacji i zwraca token JWT token dostępu, które jest używane do wywołania interfejsu API sieci web.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

### <a name="delegated-user-identity-with-openid-connect"></a>Tożsamość delegowany użytkownik za pomocą protokołu OpenID Connect

1. Użytkownik jest zalogowany do aplikacji sieci web przy użyciu usługi Azure AD (zobacz w powyższej sekcji aplikacji sieci Web w przeglądarce internetowej). Jeśli użytkownik aplikacji sieci web nie jeszcze wyraził zgodę na zezwolenie aplikacji sieci web do wywołania interfejsu API sieci web w jej imieniu, użytkownik musi wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia, a jeśli którakolwiek z tych uprawnień na poziomie administratora, zwykłego użytkownika w katalogu nie będzie można wyrazić zgodę. Ten proces zgody dotyczy tylko wielodostępne, aplikacje nie pojedynczej dzierżawy, jak aplikacja już mieć odpowiednie uprawnienia. Gdy użytkownik jest zalogowany, aplikacji sieci web odebrał tokenu Identyfikacyjnego informacje na temat użytkownika, a także kod autoryzacji.
1. Przy użyciu kodu autoryzacji, wystawiony przez usługę Azure AD, aplikacja sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, która zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (identyfikator aplikacji Identyfikator URI dla interfejsu API sieci web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji sieci web są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania tokenu JWT.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Tożsamość delegowany użytkownik za pomocą przyznawanie kodu autoryzacji OAuth 2.0

1. Użytkownik jest już zalogowany do aplikacji sieci web, w których mechanizmu uwierzytelniania jest niezależna od usługi Azure AD.
1. Aplikacja sieci web wymaga kodu autoryzacji do uzyskania tokenu dostępu, więc emituje żądanie za pośrednictwem przeglądarki do punktu końcowego autoryzacji usługi Azure AD, podając identyfikator aplikacji i identyfikator URI przekierowania dla aplikacji sieci web po pomyślnym uwierzytelnieniu. Użytkownik loguje się do usługi Azure AD.
1. Jeśli użytkownik aplikacji sieci web nie jeszcze wyraził zgodę na zezwolenie aplikacji sieci web do wywołania interfejsu API sieci web w jej imieniu, użytkownik musi wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia, a jeśli którakolwiek z tych uprawnień na poziomie administratora, zwykłego użytkownika w katalogu nie będzie można wyrazić zgodę. To wyrażenie zgody ma zastosowanie do aplikacji pojedynczych i wielu dzierżawców. W przypadku pojedynczej dzierżawy Administrator można wykonywać administratora zgody i zgody w imieniu swoich użytkowników. Można to zrobić za pomocą `Grant Permissions` znajdujący się w [witryny Azure portal](https://portal.azure.com). 
1. Po użytkownik wyraził zgodę, aplikacja sieci web otrzyma kod autoryzacji wymagane do uzyskania tokenu dostępu.
1. Przy użyciu kodu autoryzacji, wystawiony przez usługę Azure AD, aplikacja sieci web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, która zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądanego zasobu (identyfikator aplikacji Identyfikator URI dla interfejsu API sieci web).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji sieci web są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania tokenu JWT.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji sieci Web do internetowego interfejsu API scenariuszy. I, zajrzyj tu często, ponieważ często dodawane są nowe przykłady. Web [aplikacji do internetowego interfejsu API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację z punktem końcowym usługi Azure AD w wersji 1.0, zobacz [rejestrowanie aplikacji](quickstart-register-app.md).

* Pojedynczej dzierżawy — usługi tożsamości aplikacji i delegowany użytkownik tożsamości przypadków, aplikacji sieci web i interfejsu API sieci web musi być zarejestrowany w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczania dostępu do aplikacji sieci web, do jej zasobów. Jeśli jest używany typ tożsamości delegowany użytkownik musi wybierz żądane uprawnienia z aplikacji sieci web **uprawnień dotyczących innych aplikacji** menu rozwijane w witrynie Azure portal. Ten krok nie jest wymagane, jeśli jest używany typ tożsamości aplikacji.
* Wielodostępne — najpierw aplikacji sieci web jest skonfigurowana do wskazania uprawnienia, które wymaga, aby działała prawidłowo. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym po użytkownik lub administrator w katalogu docelowym powoduje zgody aplikacji, która udostępnia je do swojej organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji mogą wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie może wyrażać zgody na. Tylko administrator katalogu mogą wyrazić zgodę, do aplikacji, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraża zgodę, interfejsu API sieci web i aplikacji sieci web są zarówno zarejestrowane w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacja sieci web używa jego kod autoryzacji można uzyskać tokenu dostępu JWT, również odbiera token odświeżania tokenu JWT. Po wygaśnięciu token dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności ich ponownego zalogowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który skutkuje nowy token dostępu i token odświeżania.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o innych [typów aplikacji i scenariuszy](app-types.md)
- Dowiedz się więcej o usłudze Azure AD [podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)
