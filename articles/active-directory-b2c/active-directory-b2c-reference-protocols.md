---
title: Protokoły uwierzytelniania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak tworzyć aplikacje bezpośrednio przy użyciu protokołów, które są obsługiwane przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 083fd6b6027c78e956c133d7801a03fd9042e88d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835750"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protokoły uwierzytelniania
Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia usługi tożsamości dostępne jako usługa dla swoich aplikacji dzięki obsłudze dwóch standardowych w branży protokołów: OpenID Connect i OAuth 2.0. Usługa jest zgodny ze standardami, ale wszelkie dwie implementacje protokołów mogą występować niewielkie różnice.

Informacje przedstawione w tym przewodniku jest przydatne w przypadku pisania kodu przez bezpośredniej korespondencji oraz obsługi żądań HTTP, a nie przy użyciu biblioteki typu open source. Firma Microsoft zaleca, przeczytaj tę stronę, przed wykonaniem instrukcji zawartych w szczegóły każdego określonego protokołu. Ale jeśli już znasz z usługi Azure AD B2C, możesz przejść bezpośrednio do [podręczniki protokołu](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Podstawy
Każda aplikacja, która używa usługi Azure AD B2C należy zarejestrować w katalogu usługi B2C w [witryny Azure portal](https://portal.azure.com). Proces rejestracji aplikacji polega na zgromadzeniu kilku wartości i przypisaniu ich do aplikacji:

* **Identyfikator aplikacji** który w sposób unikatowy identyfikuje aplikację.
* A **identyfikator URI przekierowania** lub **pakietu identyfikator** który może służyć do kierowania odpowiedzi z powrotem do aplikacji.
* Kilka innych wartości specyficznych dla danego scenariusza. Aby uzyskać więcej informacji, Dowiedz się, [jak zarejestrować aplikację](active-directory-b2c-app-registration.md).

Po zarejestrowaniu aplikacji należy go komunikuje się z usługą Azure Active Directory (Azure AD), wysyłając żądania do punktu końcowego:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

W prawie wszystkie przepływy OAuth i OpenID Connect cztery strony są związane z programu exchange:

![Diagram przedstawiający cztery role OAuth w wersji 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* **Serwera autoryzacji** jest punktem końcowym usługi Azure AD. Bezpieczną obsługę niczego związane z dostępu i informacji o użytkowniku. Obsługuje ona także relacje zaufania między stronami w przepływie. Jest odpowiedzialna za weryfikowanie tożsamości użytkowników, udzielanie odbieranie prawa dostępu do zasobów i wydawania tokenów. Jest również znany jako dostawcy tożsamości.

* **Właściciel zasobu** jest zazwyczaj użytkownika końcowego. Jest to strona, która jest właścicielem danych i ma możliwości, aby umożliwić firmom dostęp do tych danych lub zasobu.

* **Klienta OAuth** aplikacja. Jest identyfikowany przez jego identyfikator aplikacji. Zazwyczaj jest innych firm, które posługują się użytkownicy końcowi. Żąda ona również tokenów z serwera autoryzacji. Właściciel zasobu musi udzielić uprawnień klienta dostępu do zasobu.

* **Serwer zasobów** jest, gdzie znajduje się zasób lub danych. Magazyn uzna serwera autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth. Używa również tokenów dostępu do elementu nośnego, aby upewnić się, że można udzielić dostępu do zasobu.

## <a name="policies-and-user-flows"></a>Zasady i użytkownika przepływów
Można przypuszczać, że zasady usługi Azure AD B2C są najważniejszych funkcji usługi. Usługa Azure AD B2C rozszerza standardowe protokoły OAuth 2.0 i OpenID Connect, wprowadzenie do zasad. Umożliwiają one znacznie bardziej niż proste uwierzytelnianie i autoryzacja usługi Azure AD B2C.

Ułatwiające konfigurowanie typowych zadań tożsamości, w portalu usługi Azure AD B2C zawiera wstępnie zdefiniowane, można skonfigurować zasady o nazwie **przepływy użytkownika**. Przepływy użytkownika pełni opisują środowiska tożsamości konsumentów, łącznie z rejestracji i logowania i profilu edycji. Przepływy użytkownika można zdefiniować w administracyjnej interfejsu użytkownika. Można je wykonać za pomocą parametru zapytania specjalne w żądaniach uwierzytelniania HTTP.

Zasady i przepływy użytkownika nie są standardowe funkcje protokołu OAuth 2.0 i OpenID Connect, więc należy poświęcić czas na ich zrozumienie. Aby uzyskać więcej informacji, zobacz [Podręcznik informacyjny przepływ użytkownika usługi Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokeny
Implementacja usługi Azure AD B2C protokołu OAuth 2.0 i OpenID Connect sprawia, że zwiększone użycie tokenów elementu nośnego, łącznie z tokenów elementu nośnego, które są reprezentowane jako tokenów sieci web JSON (tokenów Jwt). Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu.

Elementu nośnego jest każda strona, która może powodować tokenu. Usługa Azure AD muszą najpierw zostać uwierzytelnione stroną przed może odbierać tokenu elementu nośnego. Ale jeśli nie podejmuje kroki wymagane do zabezpieczania token w transmisji i przechowywania, można go przechwycony i używane przez niezamierzone innych firm.

Niektóre tokeny zabezpieczające mają wbudowane mechanizmy, które uniemożliwiają korzystanie z nich osoby nieupoważnione, ale tokenów elementu nośnego nie mają ten mechanizm. Ich musi być transportowane zabezpieczony kanał, takich jak zabezpieczeń warstwy transportu (HTTPS).

Jeśli token elementu nośnego, są przesyłane poza bezpiecznego kanału, złośliwa strona użyć ataków typu man-in--middle do uzyskania tokenu i używać go w celu uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy tokenów elementu nośnego, są przechowywane lub pamięci podręcznej do późniejszego użycia. Zawsze upewnij się, że Twoja aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób.

Kwestie związane z dodatkowego elementu nośnego tokenu zabezpieczeń, zobacz [RFC 6750 sekcji 5](https://tools.ietf.org/html/rfc6750).

Więcej informacji na temat różnych rodzajów tokenów, które są używane w usłudze Azure AD B2C są dostępne w [odwołania do tokenu usługi Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoły
Gdy wszystko będzie gotowe zapoznać się z niektórych żądań przykład, można uruchomić przy użyciu jednego z następujących samouczków. Każdy odnosi się do scenariusza określonego uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, które przepływ jest odpowiedni dla Ciebie, zapoznaj się z [typy aplikacji, można tworzyć przy użyciu usługi Azure AD B2C](active-directory-b2c-apps.md).

* [Tworzenie aplikacji mobilnych i natywnych przy użyciu protokołu OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Tworzenie aplikacji sieci web za pomocą protokołu OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Tworzenie aplikacji jednostronicowej przy użyciu niejawnego przepływu OAuth 2.0](active-directory-b2c-reference-spa.md)

