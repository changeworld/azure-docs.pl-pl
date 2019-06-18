---
title: Natywne aplikacje w usłudze Azure Active Directory
description: Opisuje co to są aplikacje natywne i podstawowe informacje dotyczące protokołu przepływu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
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
ms.openlocfilehash: a6bf24124c4b072a64ef59500b2f723ff6abbb0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545840"
---
# <a name="native-apps"></a>Aplikacje natywne

Natywne aplikacje to aplikacje, które wywoływać internetowy interfejs API w imieniu użytkownika. Ten scenariusz jest oparty na typu przydziału kodu autoryzacji OAuth 2.0 przy użyciu publicznych klienta, zgodnie z opisem w sekcji 4.1 [specyfikację OAuth 2.0](https://tools.ietf.org/html/rfc6749). Aplikacji macierzystej uzyskuje token dostępu dla użytkownika przy użyciu protokołu OAuth 2.0. Ten token dostępu jest następnie wysyłana w żądaniu w internetowym interfejsie API, które udzielają użytkownikowi autoryzacji i zwraca żądanego zasobu.

## <a name="diagram"></a>Diagram

![Natywną aplikację sieci Web diagramu interfejsu API](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokołu przepływu

Jeśli używasz biblioteki uwierzytelniania AD Większość opisanych poniżej szczegółów ma protokołu są obsługiwane, takich jak okno podręczne z przeglądarki, buforowanie tokenów oraz obsługi tokenów odświeżania.

1. Za pomocą aplikacji natywnej wysyła żądanie do punktu końcowego autoryzacji w usłudze Azure AD wyskakujące przeglądarki. To żądanie zawiera identyfikator aplikacji oraz identyfikatora URI przekierowania aplikacji macierzystej, jak pokazano w witrynie Azure portal i identyfikator URI aplikacji interfejsu API sieci web. Jeśli użytkownik nie zostało to zrobione, są monitowani o Zaloguj się ponownie
1. Usługa Azure AD uwierzytelnia użytkownika. Jeśli jest to aplikacja wielodostępna i zgoda jest wymagana do korzystania z aplikacji, użytkownik będą musieli wyrazić zgodę, jeśli ich jeszcze tego nie zrobiłeś. Po przyznaniu zgody i po pomyślnym uwierzytelnieniu usługa Azure AD wystawia odpowiedzi kod autoryzacji do identyfikatora URI przekierowania aplikacji klienta.
1. Gdy usługa Azure AD wysyła odpowiedź kodu autoryzacji do identyfikatora URI przekierowania, aplikacja kliencka zatrzymuje interakcji z przeglądarką i wyodrębnia kod autoryzacji z odpowiedzi. Przy użyciu tego kodu autoryzacji, aplikacja kliencka wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegółowe informacje o aplikacji klienta (identyfikator aplikacji i identyfikator URI przekierowania) i żądany zasób (identyfikator URI aplikacji dla internetowy interfejs API).
1. Kod autoryzacji i informacji na temat interfejsu API sieci web i aplikacji klienta są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługi Azure AD zwraca dwa tokeny: token JWT dostępu i token odświeżania tokenu JWT. Ponadto usługa Azure AD zwraca podstawowe informacje o użytkowniku, takie jak ich wyświetlaną nazwę i dzierżawy identyfikator.
1. Przy użyciu protokołu HTTPS aplikacja kliencka używa zwrócony token dostępu tokenów JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.
1. Po wygaśnięciu token dostępu, aplikacja kliencka otrzyma błąd, który wskazuje, że użytkownik musi uwierzytelnić się ponownie. Aplikacja ma token odświeżania prawidłowe, może służyć uzyskać nowy token dostępu bez wyświetlania monitu użytkownikowi zalogowanie się ponownie. Jeśli wygaśnięciu ważności tokenu odświeżania aplikacji muszą interaktywnie ponownie uwierzytelnić użytkownika.

> [!NOTE]
> Token odświeżania wydane przez usługę Azure AD może służyć do dostępu do wielu zasobów. Na przykład jeśli masz aplikację kliencką, która ma uprawnienia do wywołania dwóch interfejsów API sieci web, token odświeżania może służyć do uzyskania dostępu do innych internetowy interfejs API oraz token.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla aplikacji natywnej do scenariuszy interfejsu Web API. I wrócić tu często — często dodajemy nowe przykłady. [Aplikacja natywna do internetowego interfejsu API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację z punktem końcowym usługi Azure AD w wersji 1.0, zobacz [rejestrowanie aplikacji](quickstart-register-app.md).

* Pojedynczej dzierżawy - aplikacji natywnej i interfejsu API sieci web musi być zarejestrowana w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczenia aplikacji natywnej dostęp do swoich zasobów. Następnie aplikacja kliencka wybiera odpowiednich uprawnień, z menu rozwijanego "Uprawnienia na inne aplikacje" w witrynie Azure portal.
* Wielodostępne — najpierw aplikacji natywnej tylko zarejestrowane w dewelopera lub katalog wydawcy. Po drugie natywną aplikację skonfigurowano do wskazania uprawnienia, które wymaga, aby działała prawidłowo. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym po użytkownik lub administrator w katalogu docelowym powoduje zgody aplikacji, która udostępnia je do swojej organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji mogą wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie może wyrażać zgody na. Tylko administrator katalogu mogą wyrazić zgodę, do aplikacji, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraża zgodę, tylko interfejs API sieci web jest zarejestrowany w swojego katalogu. 

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacji natywnej używa jego kod autoryzacji można uzyskać tokenu dostępu JWT, również odbiera token odświeżania tokenu JWT. Po wygaśnięciu token dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności ich ponownego zalogowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który skutkuje nowy token dostępu i token odświeżania.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o innych [typów aplikacji i scenariuszy](app-types.md)
- Dowiedz się więcej o usłudze Azure AD [podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)
