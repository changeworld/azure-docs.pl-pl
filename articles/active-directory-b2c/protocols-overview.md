---
title: Protokoły uwierzytelniania w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Jak tworzyć aplikacje bezpośrednio przy użyciu protokołów obsługiwanych przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183909"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Usługa Azure AD B2C: protokoły uwierzytelniania
Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia tożsamość jako usługę dla aplikacji, obsługując dwa standardowe protokoły branżowe: OpenID Connect i OAuth 2.0. Usługa jest zgodna ze standardami, ale wszystkie dwie implementacje tych protokołów mogą mieć subtelne różnice.

Informacje zawarte w tym przewodniku są przydatne w przypadku pisania kodu przez bezpośrednie wysyłanie i obsługę żądań HTTP, a nie przy użyciu biblioteki open source. Zalecamy przeczytanie tej strony przed zapoznaniem się ze szczegółami każdego konkretnego protokołu. Ale jeśli jesteś już zaznajomiony z usługą Azure AD B2C, możesz przejść bezpośrednio do [przewodników odsyłaczy do protokołu.](#protocols)

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Podstawy
Każda aplikacja korzystająca z usługi Azure AD B2C musi być zarejestrowana w katalogu B2C w [witrynie Azure portal.](https://portal.azure.com) Proces rejestracji aplikacji polega na zgromadzeniu kilku wartości i przypisaniu ich do aplikacji:

* **Identyfikator aplikacji** który w sposób unikatowy identyfikuje aplikację.
* **Przekierowanie identyfikatora URI** lub **pakietu,** który może służyć do bezpośredniego odpowiedzi z powrotem do aplikacji.
* Kilka innych wartości specyficznych dla scenariusza. Aby uzyskać więcej informacji, dowiedz się, [jak zarejestrować aplikację](tutorial-register-applications.md).

Po zarejestrowaniu aplikacji komunikuje się z usługą Azure Active Directory (Azure AD) przez wysyłanie żądań do punktu końcowego:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

W prawie wszystkich przepływach OAuth i OpenID Connect cztery strony są zaangażowane w wymianę:

![Diagram przedstawiający cztery role OAuth 2.0](./media/protocols-overview/protocols_roles.png)

* Serwer **autoryzacji** jest punktem końcowym usługi Azure AD. Bezpiecznie obsługuje wszystko, co jest związane z informacjami o użytkowniku i dostępem. Obsługuje również relacje zaufania między stronami w przepływie. Jest odpowiedzialny za weryfikację tożsamości użytkownika, udzielanie i odwoływanie dostępu do zasobów oraz wystawianie tokenów. Jest również znany jako dostawca tożsamości.

* **Właścicielem zasobu** jest zazwyczaj użytkownik końcowy. Jest to strona, która jest właścicielem danych i ma prawo umożliwić stronom trzecim dostęp do tych danych lub zasobów.

* **Klient OAuth** jest twoją aplikacją. Jest identyfikowany przez jego identyfikator aplikacji. Zazwyczaj jest to strona, z którą użytkownicy końcowi wchodzą w interakcję. Żąda również tokenów z serwera autoryzacji. Właściciel zasobu musi udzielić klientowi uprawnień dostępu do zasobu.

* **Serwer zasobów** znajduje się w miejscu, w którym znajduje się zasób lub dane. Ufa serwerowi autoryzacji, aby bezpiecznie uwierzytelnić i autoryzować klienta OAuth. Używa również tokenów dostępu okaziciela, aby zapewnić, że można udzielić dostępu do zasobu.

## <a name="policies-and-user-flows"></a>Zasady i przepływy użytkowników
Prawdopodobnie zasady usługi Azure AD B2C są najważniejsze funkcje usługi. Usługa Azure AD B2C rozszerza standardowe protokoły OAuth 2.0 i OpenID Connect, wprowadzając zasady. Umożliwiają one usługi Azure AD B2C do wykonywania znacznie więcej niż proste uwierzytelnianie i autoryzacji.

Aby ułatwić konfigurowanie najczęstszych zadań tożsamości, portal usługi Azure AD B2C zawiera wstępnie zdefiniowane, konfigurowalne zasady o nazwie **przepływy użytkowników**. Przepływy użytkowników w pełni opisują środowisko tożsamości konsumenta, w tym rejestrację, logowanie i edycję profilu. Przepływy użytkownika można zdefiniować w interfejsie użytkownika administracyjnego. Można je wykonać przy użyciu specjalnego parametru zapytania w żądaniach uwierzytelniania HTTP.

Zasady i przepływy użytkowników nie są standardowymi funkcjami OAuth 2.0 i OpenID Connect, więc należy poświęcić trochę czasu, aby je zrozumieć. Aby uzyskać więcej informacji, zobacz [przewodnik dotyczący przepływu użytkownika usługi Azure AD B2C](user-flow-overview.md).

## <a name="tokens"></a>Tokeny
Implementacja usługi Azure AD B2C OAuth 2.0 i OpenID Connect w szerokim zakresie korzysta z tokenów nośnika, w tym tokenów okaziciela, które są reprezentowane jako tokeny internetowe JSON (JWTs). Token nośnika jest lekki token zabezpieczający, który udziela "nośnik" dostęp do chronionego zasobu.

Okazicielem jest każda strona, która może przedstawić token. Usługa Azure AD musi najpierw uwierzytelnić stronę, zanim będzie mogła odebrać token na nośniku. Ale jeśli wymagane kroki nie są podejmowane w celu zabezpieczenia tokenu w transmisji i przechowywania, może być przechwycony i używany przez niezamierzone strony.

Niektóre tokeny zabezpieczające mają wbudowane mechanizmy, które uniemożliwiają nieautoryzowanym stronom korzystanie z nich, ale tokeny nośnika nie mają tego mechanizmu. Muszą być transportowane w bezpiecznym kanale, takich jak zabezpieczenia warstwy transportu (HTTPS).

Jeśli token nośnika jest przesyłany poza bezpieczny kanał, złośliwa strona może użyć ataku typu man-in-the-middle, aby uzyskać token i użyć go do uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie, gdy tokeny nośnika są przechowywane lub buforowane do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokeny na okaziciela w bezpieczny sposób.

Dodatkowe zagadnienia dotyczące zabezpieczeń tokenu nośnika można znaleźć w [sekcji 5 rFC 6750](https://tools.ietf.org/html/rfc6750).

Więcej informacji na temat różnych typów tokenów, które są używane w usłudze Azure AD B2C są dostępne w [odwołaniu tokenu usługi Azure AD.](tokens-overview.md)

## <a name="protocols"></a>Protokoły
Gdy będziesz gotowy do przejrzenia niektórych przykładowych żądań, możesz rozpocząć od jednego z poniższych samouczków. Każdy z nich odpowiada określonemu scenariuszowi uwierzytelniania. Jeśli potrzebujesz pomocy w określeniu, który przepływ jest odpowiedni dla Ciebie, zapoznaj się [z typami aplikacji, które możesz utworzyć za pomocą usługi Azure AD B2C](application-types.md).

* [Tworzenie aplikacji mobilnych i natywnych przy użyciu OAuth 2.0](authorization-code-flow.md)
* [Tworzenie aplikacji internetowych przy użyciu funkcji OpenID Connect](openid-connect.md)
* [Tworzenie aplikacji jednostronicowych przy użyciu przepływu niejawnego OAuth 2.0](implicit-flow-single-page-application.md)

