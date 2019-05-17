---
title: Aplikacje sieci Web w usłudze Azure Active Directory
description: Opisuje co to są aplikacje sieci web i podstawowe informacje dotyczące protokołu przepływu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
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
ms.openlocfilehash: d15d76f4c16fa89b41ebfc10c9617c4709203d38
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544719"
---
# <a name="web-apps"></a>Aplikacje internetowe

Aplikacje sieci Web to aplikacje, które przeprowadzają uwierzytelnianie użytkownika w przeglądarce sieci web do aplikacji sieci web. W tym scenariuszu aplikacji sieci web poleca przeglądarkę użytkownika do logowania do usługi Azure AD. Usługa Azure AD, zwraca odpowiedź logowania za pośrednictwem przeglądarki przez użytkownika, który zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Ten scenariusz obsługuje logowanie jednokrotne za pomocą protokołów uwierzytelniania OpenID Connect, SAML 2.0 i WS-Federation.

## <a name="diagram"></a>Diagram

![Przebieg uwierzytelniania w przypadku aplikacji sieci web w przeglądarce](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Protokołu przepływu

1. Gdy użytkownik odwiedzi aplikacji i potrzeb, aby zalogować się, zostanie przekierowany za pośrednictwem żądania logowania do uwierzytelniania punktu końcowego w usłudze Azure AD.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie powiedzie się, tworzy token uwierzytelniania usługi Azure AD i zwraca odpowiedź Zaloguj się do adresu URL odpowiedzi aplikacji, który został skonfigurowany w witrynie Azure portal. W przypadku aplikacji produkcyjnych następujący adres URL odpowiedzi powinien być schemat HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika a usługą Azure AD, które są wymagane przez aplikację w celu zweryfikowania tokenu.
1. Aplikacja sprawdza poprawność tokenu przy użyciu publicznego klucza podpisywania i Wystawca informacji dostępnych w dokumentu metadanych Federacji usługi Azure AD. Po Aplikacja sprawdza poprawność tokenu, zaczyna nową sesję użytkownika. Ta sesja umożliwia użytkownikowi dostęp do aplikacji, dopóki nie wygaśnie.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla scenariuszy aplikacji sieci web w przeglądarce internetowej. I wyboru kopii często, jak często dodawane są nowe przykłady.

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację sieci web, zobacz [rejestrowanie aplikacji](quickstart-register-app.md).

* Pojedynczej dzierżawy — Jeśli tworzysz aplikację tylko dla Twojej organizacji, należy zarejestrować w katalogu firmy przy użyciu witryny Azure portal.
* Wielodostępne — Jeśli tworzysz aplikację, która może być używane przez użytkowników spoza organizacji, ten musi być zarejestrowana w katalogu firmy, ale musi być zarejestrowana w każdej organizacji katalogu, który będzie używany w aplikacji. Aby udostępnić aplikację w jego katalogu, możesz dołączyć procesu rejestracji dla klientów, co pozwala na wyrażanie zgody na aplikację. Po utworzeniu konta dla aplikacji, zobaczą okno dialogowe, które przedstawiono uprawnienia, których wymaga aplikacja i opcji do wyrażenia zgody. W zależności od wymagane uprawnienia administratora w innej organizacji może być konieczne wyrazić zgodę. Po użytkownik lub administrator wyraża zgodę, aplikacja jest zarejestrowana w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Sesja użytkownika wygasa po upływie okresu istnienia tokenu wystawionego przez usługę Azure AD. Aplikację można skrócić tego okresu czasu, jeśli to konieczne, takich jak wylogowywanie użytkowników, w oparciu o okresie braku aktywności. Po wygaśnięciu sesji użytkownika zostanie wyświetlony monit Zaloguj się ponownie.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [typów aplikacji i scenariuszy](app-types.md)
* Dowiedz się więcej o usłudze Azure AD [podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)
