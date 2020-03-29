---
title: Aplikacje sieci Web w usłudze Azure Active Directory
description: W tym artykule opisano, jakie są aplikacje sieci web i podstawy dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
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
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154410"
---
# <a name="web-apps"></a>Aplikacje internetowe

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje sieci Web to aplikacje uwierzytelniają użytkownika w przeglądarce sieci Web w aplikacji sieci Web. W tym scenariuszu aplikacja sieci web kieruje przeglądarkę użytkownika, aby zalogować się do usługi Azure AD. Usługa Azure AD zwraca odpowiedź logowania za pośrednictwem przeglądarki użytkownika, która zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. W tym scenariuszu obsługuje logowania przy użyciu OpenID Connect, SAML 2.0 i WS-Federation protokołów.

## <a name="diagram"></a>Diagram

![Przepływ uwierzytelniania dla przeglądarki do aplikacji sieci Web](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

1. Gdy użytkownik odwiedza aplikację i musi się zalogować, są one przekierowywane za pośrednictwem żądania logowania do punktu końcowego uwierzytelniania w usłudze Azure AD.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD tworzy token uwierzytelniania i zwraca odpowiedź logowania do adresu URL odpowiedzi aplikacji, który został skonfigurowany w witrynie Azure portal. W przypadku aplikacji produkcyjnej ten adres URL odpowiedzi powinien być https. Zwrócony token zawiera oświadczenia dotyczące użytkownika i usługi Azure AD, które są wymagane przez aplikację do sprawdzania poprawności tokenu.
1. Aplikacja sprawdza poprawność tokenu przy użyciu publicznego klucza podpisywania i informacje o wystawcy dostępne w dokumencie metadanych federacji dla usługi Azure AD. Po weryfikacji tokenu przez aplikację rozpoczyna nową sesję z użytkownikiem. Ta sesja umożliwia użytkownikowi dostęp do aplikacji, dopóki nie wygaśnie.

## <a name="code-samples"></a>Przykłady kodu

Zobacz przykłady kodu dla przeglądarki sieci web do scenariuszy aplikacji sieci web. I często sprawdzaj, ponieważ często dodawane są nowe próbki.

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację internetową, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Pojedyncza dzierżawa — jeśli budujemy aplikację tylko dla twojej organizacji, musi ona być zarejestrowana w katalogu firmy przy użyciu witryny Azure portal.
* Multi-tenant — jeśli budujesz aplikację, która może być używana przez użytkowników spoza organizacji, musi być zarejestrowana w katalogu firmy, ale również musi być zarejestrowana w katalogu każdej organizacji, która będzie korzystać z aplikacji. Aby udostępnić aplikację w ich katalogu, można dołączyć proces rejestracji dla klientów, który umożliwia im wyrażenie zgody na aplikację. Po zarejestrowaniu się w aplikacji zostaną one wyświetlone z okna dialogowego, który pokazuje uprawnienia aplikacji wymaga, a następnie opcja zgody. W zależności od wymaganych uprawnień administrator w innej organizacji może być zobowiązany do wyrażenia zgody. Gdy użytkownik lub administrator wyrazi na to zgodę, aplikacja jest rejestrowana w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Sesja użytkownika wygasa po wygaśnięciu okresu istnienia tokenu wystawionego przez usługę Azure AD. Aplikacja może skrócić ten okres w razie potrzeby, takich jak wylogowywanie użytkowników na podstawie okresu braku aktywności. Po wygaśnięciu sesji użytkownik zostanie poproszony o ponowne zalogowanie się.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [typach i scenariuszach aplikacji](app-types.md)
* Dowiedz się więcej o [podstawach uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
