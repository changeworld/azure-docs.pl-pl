---
title: Aplikacje usługi do usługi w usłudze Azure Active Directory
description: W tym artykule opisano, jakie aplikacje usługi do usługi i podstawy dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154767"
---
# <a name="service-to-service-apps"></a>Aplikacje usługi do usługi

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje usługi do usługi może być demonem lub aplikacją serwera, która musi uzyskać zasoby z internetowego interfejsu API. Istnieją dwa podkapłaty, które mają zastosowanie do tej sekcji:

- Demon, który musi wywołać internetowy interfejs API, zbudowany na typie dotacji poświadczeń klienta OAuth 2.0

    W tym scenariuszu ważne jest, aby zrozumieć kilka rzeczy. Po pierwsze interakcja użytkownika nie jest możliwe z aplikacji demona, który wymaga aplikacji mieć własną tożsamość. Przykładem aplikacji demona jest zadanie wsadowe lub usługa systemu operacyjnego działająca w tle. Ten typ aplikacji żąda tokenu dostępu przy użyciu jego tożsamości aplikacji i prezentacji jego identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i identyfikatora aplikacji identyfikator URI do usługi Azure AD. Po pomyślnym uwierzytelnieniu demon odbiera token dostępu z usługi Azure AD, który jest następnie używany do wywoływania internetowego interfejsu API.

- Aplikacja serwera (na przykład internetowy interfejs API), która musi wywołać internetowy interfejs API, zbudowany na specyfikacji wersji roboczej OAuth 2.0 w imieniu

    W tym scenariuszu można wyobrazić sobie, że użytkownik uwierzytelnił się w aplikacji macierzystej i tej aplikacji natywnej musi wywołać interfejs API sieci web. Usługa Azure AD wystawia token dostępu JWT do wywołania internetowego interfejsu API. Jeśli internetowy interfejs API musi wywołać inny podrzędny interfejs API sieci web, może użyć przepływu w imieniu, aby delegować tożsamość użytkownika i uwierzytelnić się do interfejsu API sieci web drugiej warstwy.

## <a name="diagram"></a>Diagram

![Diagram interfejsu API demona lub aplikacji serwera do sieci Web](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Tożsamość aplikacji z dotacje poświadczeń klienta OAuth 2.0

1. Po pierwsze aplikacja serwera musi uwierzytelnić się za pomocą usługi Azure AD jako siebie, bez interakcji z człowiekiem, takich jak interaktywne okno dialogowe logowania. Powoduje, że żądanie do punktu końcowego tokenu usługi Azure AD, podając poświadczenia, identyfikator aplikacji i identyfikator URI identyfikatora aplikacji.
1. Usługa Azure AD uwierzytelnia aplikację i zwraca token dostępu JWT, który jest używany do wywoływania internetowego interfejsu API.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci web używa zwróconego tokenu dostępu JWT do dodania ciągu JWT z oznaczeniem "Nośnik" w nagłówku autoryzacji żądania do internetowego interfejsu API. Internetowy interfejs API następnie sprawdza poprawność tokenu JWT, a jeśli sprawdzanie poprawności zakończy się pomyślnie, zwraca żądany zasób.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Tożsamość delegowanego użytkownika z specyfikacją roboczą OAuth 2.0 w imieniu

Przepływ omówione poniżej zakłada, że użytkownik został uwierzytelniony w innej aplikacji (takich jak aplikacja macierzysta), a jego tożsamość użytkownika została użyta do uzyskania tokenu dostępu do interfejsu API sieci web pierwszej warstwy.

1. Aplikacja natywna wysyła token dostępu do interfejsu API sieci web pierwszej warstwy.
1. Internetowy interfejs API pierwszej warstwy wysyła żądanie do punktu końcowego tokenu usługi Azure AD, podając jego identyfikator aplikacji i poświadczenia, a także token dostępu użytkownika. Ponadto żądanie jest wysyłane z parametrem on_behalf_of, który wskazuje, że internetowy interfejs API żąda nowych tokenów do wywołania podrzędnego interfejsu API sieci web w imieniu oryginalnego użytkownika.
1. Usługa Azure AD sprawdza, czy internetowy interfejs API pierwszej warstwy ma uprawnienia dostępu do internetowego interfejsu API drugiej warstwy i sprawdza poprawność żądania, zwracając token dostępu JWT i token odświeżania JWT do internetowego interfejsu API pierwszej warstwy.
1. Za pośrednictwem protokołu HTTPS interfejs API sieci web pierwszej warstwy wywołuje interfejs API sieci web drugiej warstwy, dołączając ciąg tokenu w nagłówku autoryzacji w żądaniu. Interfejs API sieci web pierwszej warstwy może nadal wywoływać internetowy interfejs API drugiej warstwy, o ile token dostępu i tokeny odświeżania są prawidłowe.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla scenariuszy interfejsu API demona lub aplikacji serwera do sieci Web: [Serwer lub Aplikacja demona do interfejsu API sieci Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Rejestracja aplikacji

* Pojedyncza dzierżawa — zarówno w przypadku tożsamości aplikacji, jak i przypadków tożsamości delegowanego użytkownika demon lub aplikacja serwera muszą być zarejestrowane w tym samym katalogu w usłudze Azure AD. Internetowy interfejs API można skonfigurować w taki sposób, aby udostępniał zestaw uprawnień, które są używane do ograniczania dostępu demona lub serwera do jego zasobów. Jeśli używany jest delegowany typ tożsamości użytkownika, aplikacja serwera musi wybrać żądane uprawnienia. Na stronie **Uprawnienia interfejsu API** dla rejestracji aplikacji po wybraniu opcji Dodaj **uprawnienie** i wybraniu rodziny interfejsów API wybierz pozycję **Uprawnienia delegowane**, a następnie wybierz swoje uprawnienia. Ten krok nie jest wymagany, jeśli używany jest typ tożsamości aplikacji.
* Wielodostępne — najpierw demon lub aplikacja serwera jest skonfigurowana do wskazywania uprawnień, które wymaga, aby były funkcjonalne. Ta lista wymaganych uprawnień jest wyświetlana w oknie dialogowym, gdy użytkownik lub administrator w katalogu docelowym wyraża zgodę na aplikację, która udostępnia ją swojej organizacji. Niektóre aplikacje wymagają tylko uprawnień na poziomie użytkownika, na które może wyrazić zgodę każdy użytkownik w organizacji. Inne aplikacje wymagają uprawnień na poziomie administratora, na które użytkownik w organizacji nie może wyrazić zgody. Tylko administrator katalogu może wyrazić zgodę na aplikacje, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyrazi na to zgodę, oba internetowe interfejsy API są rejestrowane w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy pierwsza aplikacja używa swojego kodu autoryzacji, aby uzyskać token dostępu JWT, odbiera również token odświeżania JWT. Po wygaśnięciu tokenu dostępu token odświeżania może służyć do ponownego uwierzytelnienia użytkownika bez monitowania o poświadczenia. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, co powoduje nowy token dostępu i odświeżanie tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [typach i scenariuszach aplikacji](app-types.md)
- Dowiedz się więcej o [podstawach uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
