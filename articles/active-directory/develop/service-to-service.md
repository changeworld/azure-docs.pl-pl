---
title: Aplikacje do usługi w usłudze Azure Active Directory
description: Opisuje jakie aplikacje do usługi i podstawowe informacje na przepływ protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 11/07/2018
ms.author: v-junlch
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: e0ced89ce97d5f22270d9968fdeb0ddb3fad1e4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251996"
---
# <a name="service-to-service-apps"></a>Aplikacje do usługi

Aplikacje do usługi mogą być demona lub serwera przez użytkowników aplikacji, serwer musi pobrać zasoby z internetowego interfejsu API. Istnieją dwa scenariusze podrzędnych, które są stosowane do tej sekcji:

- Demon, który musi wywołać interfejs API sieci web, oparta na typ przydziału poświadczeń klienta OAuth 2.0

    W tym scenariuszu należy zrozumieć kilka rzeczy. Po pierwsze interakcji z użytkownikiem nie jest możliwe za pomocą aplikacji demona, którego wymaga aplikacja miała własną tożsamości. Przykład aplikacji demona to zadanie wsadowe lub usługa systemu operacyjnego, który jest uruchomiona w tle. Aplikacje tego typu żądania tokenu dostępu przy użyciu tożsamości aplikacji i zaprezentowanie jej identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i aplikacji identyfikator URI do usługi Azure AD. Po pomyślnym uwierzytelnieniu demona odbiera token dostępu z usługi Azure AD, która jest następnie używany do wywoływania interfejsu API sieci web.

- Aplikacji serwera (na przykład internetowy interfejs API), który musi wywołać internetowego interfejsu API, oparte na specyfikacji wersji roboczej OAuth 2.0 "w imieniu"

    W tym scenariuszu Załóżmy, że użytkownik został uwierzytelniony w aplikacji natywnej i tej aplikacji natywnej musi wywołać interfejs API sieci web. Usługa Azure AD wystawia token JWT dostępu do wywołania interfejsu API sieci web. Jeśli internetowy interfejs API musi wywołać inny podrzędny interfejs API sieci web, służy w imieniu użytkownika z usługi flow do delegowania tożsamości użytkownika i uwierzytelniania interfejsu API sieci web drugiej warstwy.

## <a name="diagram"></a>Diagram

![Demon lub aplikację serwera do diagramu interfejsu API sieci Web](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol przepływu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udzielanie tożsamości aplikacji przy użyciu poświadczeń klienta OAuth 2.0

1. Najpierw aplikacja serwera musi uwierzytelnić za pomocą usługi Azure AD w swoim imieniu, bez interwencji człowieka takich jak interakcyjne okno logowania jednokrotnego. Kieruje żądanie do usługi Azure AD punktu końcowego tokenu, podanie poświadczeń, Identyfikatora aplikacji i identyfikator URI aplikacji.
1. Usługa Azure AD uwierzytelnia aplikacji i zwraca token JWT token dostępu, które jest używane do wywołania interfejsu API sieci web.
1. Przy użyciu protokołu HTTPS aplikacja internetowa używa zwrócony token dostępu JWT do dodawania ciągu JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci web sprawdza poprawność tokenu JWT i, jeśli weryfikacja zakończy się pomyślnie, zwraca żądanego zasobu.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Tożsamość delegowany użytkownik za pomocą protokołu OAuth 2.0 "w imieniu" wersji roboczej specyfikacji

Przepływ omówiono poniżej przyjęto założenie, że użytkownik został uwierzytelniony w innej aplikacji (np. aplikacji natywnej), a ich tożsamość użytkownika został użyty do uzyskania tokenu dostępu do interfejsu API sieci web pierwszego rzędu.

1. Aplikacji natywnej wysyła ten token dostępu do interfejsu API sieci web pierwszego rzędu.
1. Pierwszego rzędu interfejsu API sieci web wysyła żądanie do usługi Azure AD punktu końcowego tokenu, podając jego Identyfikatora aplikacji i poświadczenia, a także tokenu dostępu. Ponadto, żądanie jest wysyłane za pomocą on_behalf_of parametrem, który wskazuje internetowy interfejs API żąda nowych tokenów do wywoływania podrzędnego internetowego interfejsu API w imieniu użytkownika oryginalnego.
1. Usługi Azure AD sprawdza pierwszego rzędu internetowy interfejs API ma uprawnienia do dostępu do drugiej warstwy sieci web interfejsu API i weryfikuje żądanie zwracania tokenu JWT dostępu i token JWT odświeżenia tokenu do pierwszego rzędu internetowego interfejsu API.
1. Przy użyciu protokołu HTTPS pierwszego rzędu internetowy interfejs API wywołuje internetowy interfejs API drugiej warstwy, dodając ciąg tokenu w nagłówku autoryzacji w żądaniu. Interfejsu API sieci web pierwszego rzędu, można w dalszym ciągu wywołania interfejsu API sieci web drugiej warstwy, tak długo, jak uzyskać token dostępu i tokenów odświeżania są prawidłowe.

## <a name="code-samples"></a>Przykłady kodu

Demon lub aplikację serwera pod kątem scenariuszy interfejsu Web API można znaleźć przykłady kodu. I wyboru kopii często, jak często dodawane są nowe przykłady. [Serwera lub demon aplikacji do internetowego interfejsu API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Rejestracja aplikacji

- Pojedynczej dzierżawy - tożsamość aplikacji i delegowany użytkownik przypadków tożsamości demona lub serwera aplikacji musi być zarejestrowana w tym samym katalogu w usłudze Azure AD. Interfejs API sieci web można skonfigurować do udostępnienia zestaw uprawnień, które są używane do ograniczenia demona lub serwera dostępu do jego zasobów. Jeśli jest używany typ tożsamości delegowany użytkownik aplikacji serwera musi wybierz żądane uprawnienia z menu rozwijanego "Uprawnienia na inne aplikacje" w witrynie Azure portal. Ten krok nie jest wymagane, jeśli jest używany typ tożsamości aplikacji.
- Wielodostępne — demon lub serwera aplikacji i jest skonfigurowany do wskazania uprawnienia, które wymaga, aby działała prawidłowo. Ta lista wymaganych uprawnień jest wyświetlany w oknie dialogowym po użytkownik lub administrator w katalogu docelowym powoduje zgody aplikacji, która udostępnia je do swojej organizacji. Niektóre aplikacje wymagają tylko uprawnienia na poziomie użytkownika, które każdy użytkownik w organizacji mogą wyrazić zgodę na. Inne aplikacje wymagają uprawnień na poziomie administratora, które użytkownik w organizacji nie może wyrażać zgody na. Tylko administrator katalogu mogą wyrazić zgodę, do aplikacji, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraża zgodę, oba interfejsy API sieci web są zarejestrowane w usłudze swojego katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Podczas pierwszej aplikacji używa jego kod autoryzacji można uzyskać tokenu dostępu JWT, również odbiera token odświeżania tokenu JWT. Po wygaśnięciu token dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez monitowania użytkownika o podanie poświadczeń. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, który skutkuje nowy token dostępu i token odświeżania.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o innych [typów aplikacji i scenariuszy](app-types.md)
- Dowiedz się więcej o usłudze Azure AD [podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)

