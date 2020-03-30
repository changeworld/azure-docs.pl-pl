---
title: Aplikacje natywne w usłudze Azure Active Directory
description: W tym artykule opisano, jakie są aplikacje natywne i podstawy dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154801"
---
# <a name="native-apps"></a>Aplikacje natywne

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje natywne to aplikacje, które wywołują internetowy interfejs API w imieniu użytkownika. Ten scenariusz jest zbudowany na OAuth 2.0 kod autoryzacji typu dotacji z klientem publicznym, zgodnie z opisem w sekcji 4.1 [specyfikacji OAuth 2.0](https://tools.ietf.org/html/rfc6749). Natywna aplikacja uzyskuje token dostępu dla użytkownika przy użyciu protokołu OAuth 2.0. Ten token dostępu jest następnie wysyłany w żądaniu do internetowego interfejsu API, który autoryzuje użytkownika i zwraca żądany zasób.

## <a name="diagram"></a>Diagram

![Natywna aplikacja do diagramu interfejsu API sieci Web](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

Jeśli używasz bibliotek uwierzytelniania usługi AD, większość szczegółów protokołu opisanych poniżej są obsługiwane dla Ciebie, takich jak wyskakujące okienka przeglądarki, buforowanie tokenów i obsługi tokenów odświeżania.

1. Za pomocą okna podręcznego przeglądarki natywnej aplikacji sprawia, że żądanie do punktu końcowego autoryzacji w usłudze Azure AD. To żądanie obejmuje identyfikator aplikacji i identyfikator URI przekierowania aplikacji macierzystej, jak pokazano w witrynie Azure portal i identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API. Jeśli użytkownik jeszcze się nie zalogował, zostanie wyświetlony monit o ponowne zalogowanie się
1. Usługa Azure AD uwierzytelnia użytkownika. Jeśli jest to aplikacja wielodostępna i zgoda jest wymagana do korzystania z aplikacji, użytkownik będzie musiał wyrazić zgodę, jeśli jeszcze tego nie zrobił. Po udzieleniu zgody i po pomyślnym uwierzytelnieniu usługa Azure AD wystawia odpowiedź kodu autoryzacji z powrotem do identyfikatora URI przekierowania aplikacji klienckiej.
1. Gdy usługa Azure AD wystawia odpowiedź kodu autoryzacji z powrotem do identyfikatora URI przekierowania, aplikacja kliencka zatrzymuje interakcję z przeglądarką i wyodrębnia kod autoryzacji z odpowiedzi. Korzystając z tego kodu autoryzacji, aplikacja kliencka wysyła żądanie do punktu końcowego tokenu usługi Azure AD, które zawiera kod autoryzacji, szczegóły dotyczące aplikacji klienckiej (identyfikator aplikacji i identyfikator przekierowania URI) oraz żądany zasób (identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API).
1. Kod autoryzacji i informacje o aplikacji klienckiej i interfejsie API sieci Web są sprawdzane przez usługę Azure AD. Po pomyślnym sprawdowaniu poprawności usługa Azure AD zwraca dwa tokeny: token dostępu JWT i token odświeżania JWT. Ponadto usługa Azure AD zwraca podstawowe informacje o użytkowniku, takie jak ich nazwa wyświetlana i identyfikator dzierżawy.
1. Za pośrednictwem protokołu HTTPS aplikacja kliencka używa zwróconego tokenu dostępu JWT do dodania ciągu JWT z oznaczeniem "Nośnik" w nagłówku autoryzacji żądania do internetowego interfejsu API. Internetowy interfejs API następnie sprawdza poprawność tokenu JWT, a jeśli sprawdzanie poprawności zakończy się pomyślnie, zwraca żądany zasób.
1. Po wygaśnięciu tokenu dostępu aplikacja kliencka otrzyma błąd, który wskazuje, że użytkownik musi uwierzytelnić się ponownie. Jeśli aplikacja ma prawidłowy token odświeżania, może służyć do uzyskania nowego tokenu dostępu bez monitowania użytkownika, aby zalogować się ponownie. Jeśli token odświeżania wygaśnie, aplikacja będzie musiała ponownie interaktywnie uwierzytelnić użytkownika.

> [!NOTE]
> Token odświeżania wystawiony przez usługę Azure AD może służyć do uzyskiwania dostępu do wielu zasobów. Na przykład jeśli masz aplikację kliencką, która ma uprawnienia do wywoływania dwóch interfejsów API sieci web, token odświeżania może służyć do uzyskania tokenu dostępu do innego interfejsu API sieci web, jak również.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla scenariuszy natywnych aplikacji do interfejsu API sieci Web. I często sprawdzamy - często dodajemy nowe próbki. [Natywna aplikacja do interfejsu API sieci Web](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację w punkcie końcowym usługi Azure AD w wersji 1.0, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Pojedyncza dzierżawa — zarówno aplikacja natywna, jak i internetowy interfejs API muszą być zarejestrowane w tym samym katalogu w usłudze Azure AD. Internetowy interfejs API można skonfigurować w taki sposób, aby udostępniał zestaw uprawnień, które są używane do ograniczania dostępu aplikacji macierzystej do jej zasobów. Następnie aplikacja kliencka wybiera żądane uprawnienia z menu rozwijanego "Uprawnienia do innych aplikacji" w witrynie Azure portal.
* Wielodostępne — najpierw aplikacja natywna tylko kiedykolwiek zarejestrowana w katalogu dewelopera lub wydawcy. Po drugie aplikacja natywna jest skonfigurowana do wskazywania uprawnień, które wymaga, aby były funkcjonalne. Ta lista wymaganych uprawnień jest wyświetlana w oknie dialogowym, gdy użytkownik lub administrator w katalogu docelowym wyraża zgodę na aplikację, która udostępnia ją swojej organizacji. Niektóre aplikacje wymagają tylko uprawnień na poziomie użytkownika, na które może wyrazić zgodę każdy użytkownik w organizacji. Inne aplikacje wymagają uprawnień na poziomie administratora, na które użytkownik w organizacji nie może wyrazić zgody. Tylko administrator katalogu może wyrazić zgodę na aplikacje, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyrazi na to zgodę, w ich katalogu jest rejestrowany tylko internetowy interfejs API. 

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy natywna aplikacja używa swojego kodu autoryzacji, aby uzyskać token dostępu JWT, odbiera również token odświeżania JWT. Po wygaśnięciu tokenu dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez konieczności ponownego logowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, co powoduje nowy token dostępu i odświeżanie tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [typach i scenariuszach aplikacji](app-types.md)
- Dowiedz się więcej o [podstawach uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
