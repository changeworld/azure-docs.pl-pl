---
title: Dowiedz się więcej o protokoły autoryzacji obsługiwanych przez usługę Azure AD w wersji 2.0 | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący protokołów obsługiwanych przez punkt końcowy usługi Azure AD w wersji 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b50d04f843e86f5af8ccd32589a540e38e6e47df
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502937"
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>w wersji 2.0 protokołów: OAuth 2.0 i OpenID Connect
Punkt końcowy v2.0 można używać usługi Azure AD dla tożsamości as-a-service za pomocą ze standardowych protokołów uwierzytelniania OpenID Connect i OAuth 2.0. Gdy usługa jest zgodny ze standardami, może to być niewielkich różnic między implementacjami dwóch tych protokołów. W tym miejscu informacje będą przydatne, jeśli istnieje możliwość pisania kodu przez bezpośrednie wysyłanie i obsługa protokołu HTTP żądania lub 3 biblioteki typu open source innych firm, a nie przy użyciu jednej z naszych [Otwórz źródło biblioteki](active-directory-v2-libraries.md).

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure Active Directory i funkcje są obsługiwane przez punkt końcowy w wersji 2.0. Aby ustalić, należy użyć punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

## <a name="the-basics"></a>Podstawowe informacje
W prawie wszystkie przepływy OAuth i OpenID Connect istnieją cztery strony związane z programu exchange:

![Role uwierzytelniania OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* **Serwera autoryzacji** jest punktem końcowym v2.0. Jest odpowiedzialny za zapewnienie tożsamości użytkownika, przyznawania odbieranie prawa dostępu do zasobów i wydawania tokenów. Jest także znana jako dostawcy tożsamości — obsługuje bezpieczne nic wspólnego z informacji o użytkowniku, ich dostęp i relacje zaufania między stronami w przepływie.
* **Właściciel zasobu** jest zwykle przez użytkownika końcowego. Jest to strona, która jest właścicielem danych i zasilania, aby umożliwić firmom dostęp do danych lub zasobów.
* **Klienta OAuth** aplikacja, identyfikowane przez jego identyfikator aplikacji. Zazwyczaj jest to strona, która wchodzi w interakcje użytkownika końcowego z, a żądania tokenów z serwera autoryzacji. Klient musi otrzymać uprawnienia dostępu do zasobu przez właściciela zasobów.
* **Serwer zasobów** jest, gdzie znajduje się zasób lub danych. Zaufany serwer autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth i używa access_tokens elementu nośnego, aby upewnić się, że można udzielić dostępu do zasobu.

## <a name="app-registration"></a>Rejestracja aplikacji
Każda aplikacja, która korzysta z punktu końcowego v2.0 będzie muszą być zarejestrowani w [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przed jej mogą wchodzić w interakcje przy użyciu uwierzytelniania OAuth lub OpenID Connect. Proces rejestracji aplikacji będzie zbierać i przypisywanie kilka wartości do aplikacji:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację
* A **identyfikator URI przekierowania** lub **identyfikator pakietu** który może służyć do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla danego scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Punkty końcowe
Po zarejestrowaniu aplikacji komunikuje się z usługą Azure AD, wysyłając żądania do punktu końcowego v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Gdzie `{tenant}` może mieć jedną z czterech różnych wartości:

| Wartość | Opis |
| --- | --- |
| `common` |Umożliwia użytkownikom osobistych kont Microsoft i konto służbowe z usługi Azure Active Directory, aby zalogować się do aplikacji. |
| `organizations` |Umożliwia tylko użytkownicy z konto służbowe z usługi Azure Active Directory, aby zalogować się do aplikacji. |
| `consumers` |Umożliwia tylko użytkownicy z osobistych kont Microsoft (MSA), aby zalogować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` |Umożliwia tylko użytkownicy z konto służbowe z określonego dzierżawy usługi Azure Active Directory, aby zalogować się do aplikacji. Przyjazna nazwa domeny dzierżawy usługi Azure AD albo identyfikator guid dzierżawy może służyć. |

Aby uzyskać więcej informacji na temat sposobu interakcji z tymi punktami końcowymi wybierz poniżej typ danej aplikacji.

## <a name="tokens"></a>Tokeny
Implementacja v2.0 OAuth 2.0 i OpenID Connect, należy zwiększone użycie tokenów elementu nośnego, łącznie z tokenów elementu nośnego reprezentowane jako tokenów Jwt. Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" to każda strona, która może powodować tokenu. Chociaż strona muszą najpierw zostać uwierzytelnione za pomocą usługi Azure AD, aby otrzymać token elementu nośnego, jeśli nie podejmuje kroki wymagane do zabezpieczania token w transmisji i przechowywania, można przechwycony i używane przez niezamierzone innych firm. Podczas gdy niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwia ich użycie przez osoby nieupoważnione, tokenów elementu nośnego nie mają ten mechanizm i musi być transportowane bezpiecznego kanału, takie jak transport layer security (HTTPS). Jeśli token elementu nośnego jest przekazywane w zabezpieczeniu, ataków typu man-in środkowej atak może służyć przez złośliwa strona do uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy przechowywania lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że Twoja aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Dalsze szczegóły różnego rodzaju tokeny używane w punkcie końcowym v2.0 jest dostępna w [odwołania do tokenu punktu końcowego v2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protokoły
Gdy wszystko będzie gotowe wyświetlić niektóre przykładowe żądania, wprowadzenie do jednej z poniższych samouczków. Każdy z nich odnosi się do scenariusza określonego uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, czyli przepływ odpowiednie dla Ciebie, zapoznaj się z [typy aplikacji, które można skompilować przy użyciu v2.0](active-directory-v2-flows.md).

* [Umożliwia tworzenie natywnych aplikacji przy użyciu protokołu OAuth 2.0 i mobilnych](active-directory-v2-protocols-oauth-code.md)
* [Twórz Web aplikacje z Open ID Connect](active-directory-v2-protocols-oidc.md)
* [Tworzenie aplikacji jednostronicowej przy użyciu niejawnego przepływu OAuth 2.0](active-directory-v2-protocols-implicit.md)
* [Demony kompilacji lub po stronie procesów serwera przy użyciu poświadczeń klienta OAuth 2.0 przepływu](active-directory-v2-protocols-oauth-client-creds.md)
* [Uzyskiwanie tokenów Web API za pomocą protokołu OAuth 2.0 w imieniu z przepływu](active-directory-v2-protocols-oauth-on-behalf-of.md)
