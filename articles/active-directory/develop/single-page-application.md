---
title: Aplikacje jednej strony w usłudze Azure Active Directory
description: Opisuje jakie aplikacje jednej strony (źródła) są oraz podstawowe informacje dotyczące protokołu przepływu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
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
ms.openlocfilehash: 1f6f66779bec9ed4e38e5a662c2d3728ba2034b6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545299"
---
# <a name="single-page-applications"></a>Aplikacje jednej strony

Aplikacje jednej strony (źródła) zazwyczaj są ustrukturyzowane jako Warstwa prezentacji JavaScript (fronton), który działa w przeglądarce i internetowy interfejs API zaplecza jest uruchamiany na serwerze, który implementuje logikę biznesową aplikacji. Aby dowiedzieć się więcej o niejawnym przyznawaniu autoryzacji i pomóc w podjęciu decyzji, czy jest odpowiedni dla scenariusza aplikacji, zobacz [zrozumienie OAuth2 niejawne udzielić przepływ w usłudze Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

W tym scenariuszu, gdy użytkownik się zaloguje, JavaScript frontonu używa zakończenia [Active Directory Authentication Library dla języka JavaScript (biblioteki ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) i niejawnym przyznawaniu autoryzacji można uzyskać tokenu Identyfikatora (id_token) z usługi Azure AD. Token jest buforowana i klient dołącza go do żądania jako token elementu nośnego w wywołaniach jego interfejsu API sieci Web zaplecza, która jest zabezpieczony za pomocą oprogramowania pośredniczącego OWIN.

## <a name="diagram"></a>Diagram

![Diagram aplikacji jednostronicowych](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Protokołu przepływu

1. Użytkownik przechodzi do aplikacji sieci web.
1. Aplikacja zwraca JavaScript frontonu (Warstwa prezentacji) w przeglądarce.
1. Zainicjowaniu logowania użytkownika, na przykład, klikając Link umożliwiający zalogowanie się. Przeglądarka wysyła GET do punktu końcowego autoryzacji usługi Azure AD do żądania tokenu Identyfikatora. To żądanie zawiera adres URL Identyfikatora i odpowiedź aplikacji w parametrach zapytania.
1. Usługa Azure AD sprawdza poprawność adresu URL odpowiedzi względem zarejestrowanych adres URL odpowiedzi, który został skonfigurowany w witrynie Azure portal.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie się powiedzie, usługi Azure AD tworzy token Identyfikatora i zwraca go jako fragmentu adresu URL (#), aby adres URL odpowiedzi aplikacji. W przypadku aplikacji produkcyjnych następujący adres URL odpowiedzi powinien być schemat HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika a usługą Azure AD, które są wymagane przez aplikację w celu zweryfikowania tokenu.
1. Kod klienta JavaScript w przeglądarce wyodrębnia token z odpowiedzi do użycia w zabezpieczanie wywołań do aplikacji sieci web, czy zakończyć interfejsu API z powrotem.
1. Przeglądarka wywołuje aplikacji sieci web interfejsu API z powrotem kończyć się znakiem tokenu Identyfikacyjnego w nagłówku autoryzacji. Usługa uwierzytelniania usługi Azure AD wystawia token Identyfikatora, który może służyć jako token elementu nośnego, jeśli zasób jest taki sam jak identyfikator klienta (w tym przypadku to wartość true, ponieważ internetowy interfejs API zaplecza połączonej aplikacji).

## <a name="code-samples"></a>Przykłady kodu

Zobacz [przykłady dla scenariuszy aplikacji jednostronicowej kodu](sample-v1-code.md#single-page-applications). Pamiętaj wrócić tu często, jak często dodawane są nowe przykłady.

## <a name="app-registration"></a>Rejestracja aplikacji

* Pojedynczej dzierżawy — Jeśli tworzysz aplikację tylko dla Twojej organizacji, należy zarejestrować w katalogu firmy przy użyciu witryny Azure portal.
* Wielodostępne — Jeśli tworzysz aplikację, która może być używane przez użytkowników spoza organizacji, ten musi być zarejestrowana w katalogu firmy, ale musi być zarejestrowana w każdej organizacji katalogu, który będzie używany w aplikacji. Aby udostępnić aplikację w jego katalogu, możesz dołączyć procesu rejestracji dla klientów, co pozwala na wyrażanie zgody na aplikację. Po utworzeniu konta dla aplikacji, zobaczą okno dialogowe, które przedstawiono uprawnienia, których wymaga aplikacja i opcji do wyrażenia zgody. W zależności od wymagane uprawnienia administratora w innej organizacji może być konieczne wyrazić zgodę. Po użytkownik lub administrator wyraża zgodę, aplikacja jest zarejestrowana w ich katalogu.

Po zarejestrowaniu aplikacji musi być skonfigurowany do używania protokołu przyznawanie niejawne protokołu OAuth 2.0. Domyślnie ten protokół jest wyłączone dla aplikacji. Aby włączyć protokół przyznawanie niejawne protokołu OAuth2 dla aplikacji, Edytuj manifest aplikacji w witrynie Azure portal, a następnie ustaw wartość "oauth2AllowImplicitFlow" na true. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](reference-app-manifest.md).

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Za pomocą ADAL.js może ułatwić realizację:

* odświeżanie tokenu wygasłe
* żądania tokenu dostępu do wywołania zasobu internetowego interfejsu API

Po pomyślnym uwierzytelnieniu usługa Azure AD zapisuje plik cookie w przeglądarce użytkownika w celu ustanowienia sesji. Należy pamiętać, że sesja istnieje między użytkownikiem a usługa Azure AD (nie między użytkownikiem i aplikacji sieci web). Po wygaśnięciu token ADAL.js korzysta z tej sesji dyskretnie uzyskać inny token. ADAL.js używa ukrytego elementu iFrame, aby wysyłać i odbierać żądania przy użyciu protokołu przyznawanie niejawne protokołu OAuth. ADAL.js umożliwia również ten sam mechanizm dyskretnie uzyskiwanie tokenów dostępu dla innych zasobów interfejsu API sieci web, wywoływanych przez aplikację, tak długo, jak te zasoby pomocy technicznej współużytkowanie zasobów między źródłami (cors), są rejestrowane w katalogu użytkownika, a wszelkie wymagane zgody zostało podane przez użytkownika podczas logowania.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [typów aplikacji i scenariuszy](app-types.md)
* Dowiedz się więcej o usłudze Azure AD [podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)
