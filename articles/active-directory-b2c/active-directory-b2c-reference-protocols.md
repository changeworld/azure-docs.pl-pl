---
title: Protokoły uwierzytelniania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak tworzyć aplikacje za pomocą protokołów, które są obsługiwane przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 09b76cd2235663d76b9973ff722ec6a515c30285
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709686"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Usługi Azure AD B2C: Protokoły uwierzytelniania
Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia tożsamości jako usługa dla aplikacji dzięki obsłudze dwóch standardowych protokołach branżowych: OpenID Connect i OAuth 2.0. Usługa jest zgodny ze standardami, ale dwóch implementacjami tych protokołów mogą mieć niewielkie różnice. 

Informacje przedstawione w tym przewodniku jest przydatne, jeśli wpisz swój kod bezpośrednio wysyłając i obsługi żądań HTTP, a nie za pomocą biblioteki typu open source. Firma Microsoft zaleca czytać tę stronę, aby przejść do szczegółów każdego z protokołów. Ale jeśli już znasz usługi Azure AD B2C, można przejść bezpośrednio do [podręczniki protokołu](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Podstawy
Każda aplikacja, która używa usługi Azure AD B2C musi być zarejestrowana w katalogu usługi B2C w [portalu Azure](https://portal.azure.com). Proces rejestracji aplikacji polega na zgromadzeniu kilku wartości i przypisaniu ich do aplikacji:

* **Identyfikator aplikacji** który w sposób unikatowy identyfikuje aplikację.
* A **identyfikator URI przekierowania** lub **pakietu identyfikator** który może służyć do kierowania odpowiedzi z powrotem do aplikacji.
* Kilka innych wartości specyficzne dla scenariusza. Aby uzyskać więcej informacji, Dowiedz się [jak zarejestrować aplikację](active-directory-b2c-app-registration.md).

Po zarejestrowaniu aplikacji komunikacji z usługą Azure Active Directory (Azure AD), wysyłając żądania do punktu końcowego:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

W prawie wszystkie przepływy OAuth i OpenID Connect cztery strony są związane z programu exchange:

![Role uwierzytelniania OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* **Serwera autoryzacji** jest punktem końcowym usługi Azure AD. Bezpieczną obsługę niczego dotyczące dostępu i informacji o użytkowniku. Obsługuje ona również relacje zaufania między stronami w strumieniu. Jest on odpowiedzialny za weryfikowania tożsamości użytkowników, udzielanie odwoływanie dostępu do zasobów i wystawiania tokenów. Jest nazywana dostawcy tożsamości.

* **Właściciel zasobu** jest zwykle użytkownika końcowego. Jest strona, która jest właścicielem danych, i ma zasilania umożliwiają innym firmom dostęp do tych danych lub zasobu.

* **Klienta OAuth** jest aplikacji. Jest identyfikowany przez jego identyfikator aplikacji. Zazwyczaj jest strona, która użytkownikom końcowym interakcję z. Żąda także tokenów z serwera autoryzacji. Właściciel zasobu musi udzielić uprawnień klienta do dostępu do zasobu.

* **Serwer zasobów** jest, w którym znajduje się zasobów lub danych. Zaufany serwer autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth. Używa również tokenów elementu nośnego dostępu, aby upewnić się, można otrzymać dostęp do zasobu.

## <a name="policies"></a>Zasady
Można przypuszczać, że zasady usługi Azure AD B2C są najważniejszych funkcji usługi. Usługa Azure AD B2C rozszerza standardowych protokołów uwierzytelniania OAuth 2.0 i OpenID Connect dzięki zastosowaniu zasad. Umożliwiają one usługi Azure AD B2C przeprowadzenie znacznie bardziej niż prostego uwierzytelniania i autoryzacji. 

Zasady pełni opisano funkcje tożsamości użytkownika, w tym rejestrację, logowanie i profilu edycji. Zasady można zdefiniować w administracyjnej interfejsu użytkownika. Mogą one wykonywane za pomocą parametru zapytania specjalne w żądania uwierzytelniania HTTP. 

Zasady nie są standardowe funkcje OAuth 2.0 i OpenID Connect, więc należy poświęcić czas na ich zrozumienie. Aby uzyskać więcej informacji, zobacz [przewodniku zasad usługi Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokeny
Wdrażanie usługi Azure AD B2C OAuth 2.0 i OpenID Connect sprawia, że zwiększone użycie tokenów elementu nośnego, łącznie z tokenów elementu nośnego, które są reprezentowane jako tokenów sieci web JSON (Jwt). Token elementu nośnego jest tokenem zabezpieczającym lekkie, która udziela dostępu "bearer" do chronionego zasobu.

Przenoszącej jest każda strona, która może ona powodować tokenu. Usługi Azure AD muszą najpierw zostać uwierzytelnione strona przed może odbierać tokenu elementu nośnego. Ale jeśli wymagane kroki nie są brane do zabezpieczania token w transmisji i przechowywania, może być przechwycony i używane przez niezamierzone strony.

Niektóre tokeny zabezpieczające mają wbudowane mechanizmy, które uniemożliwić ich użycie nieupoważnione, ale tokenów elementu nośnego nie mają ten mechanizm. Muszą one być transportowane w bezpiecznego kanału, takie jak zabezpieczeń warstwy transportu (HTTPS). 

Token elementu nośnego, są przesyłane poza bezpiecznego kanału, strony złośliwych służy atak typu man-in--middle uzyskać token i używać go w celu uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie, gdy tokenów elementu nośnego, są przechowywane lub w pamięci podręcznej do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób.

Dla elementu nośnego dodatkowe zagadnienia dotyczące tokenu zabezpieczeń, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Więcej informacji na temat różnych typów tokenów, które są używane w usłudze Azure AD B2C są dostępne w [odwołania do tokenu usługi Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoły
Gdy wszystko jest gotowe do przejrzenia niektórych żądań przykładzie, można uruchomić z jednym z następujących samouczków. Każdy odpowiada scenariusz, w szczególności uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, które przebieg jest odpowiednie dla Ciebie, zapoznaj się [typy aplikacji, można tworzyć za pomocą usługi Azure AD B2C](active-directory-b2c-apps.md).

* [Tworzenie aplikacji mobilnych i natywnego przy użyciu protokołu OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Tworzenie aplikacji sieci web przy użyciu protokołu OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Tworzenie aplikacji jednej strony przy użyciu niejawnego przepływu OAuth 2.0](active-directory-b2c-reference-spa.md)

