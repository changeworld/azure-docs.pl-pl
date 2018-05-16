---
title: Więcej informacji na temat autoryzacji protokoły obsługiwane przez usługę Azure AD w wersji 2.0 | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący protokołów obsługiwanych przez punktu końcowego v2.0 usługi Azure AD.
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
ms.openlocfilehash: 7c6031bb135c48a8d58f61c3c96bf18e817809ba
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>w wersji 2.0 protokołów - OAuth 2.0 & OpenID Connect
Punktu końcowego v2.0 można używać usługi Azure AD identity jako — usługa z branży standardowe protokoły OpenID Connect i OAuth 2.0. Gdy usługa jest zgodny ze standardami, może to mieć niewielkie różnice między dwoma implementacjami tych protokołów. Informacje w tym miejscu będą przydatne w przypadku należy napisać kod bezpośrednio wysyłając & obsługi protokołu HTTP żądania lub 3 biblioteki typu open source firmy, a nie przy użyciu jednej z naszych [Otwórz źródło biblioteki](active-directory-v2-libraries.md).

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariuszy i funkcji obsługiwanych przez punktu końcowego v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

## <a name="the-basics"></a>Podstawy
W niemal wszystkich przepływów OAuth i OpenID Connect obejmuje cztery strony programu exchange:

![Role uwierzytelniania OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* **Serwera autoryzacji** jest punktem końcowym v2.0. Jest on odpowiedzialny za zapewnienie tożsamości użytkownika, udzielanie i odwoływanie dostępu do zasobów i wystawiania tokenów. Jest także znana jako dostawca tożsamości — bezpieczną obsługę związek z informacji o użytkowniku, ich dostęp i relacje zaufania między stronami w strumieniu.
* **Właściciel zasobu** jest zwykle przez użytkownika. Jest strona, która jest właścicielem danych i zasilania umożliwiają innym firmom dostęp do danych lub zasobu.
* **Klienta OAuth** jest aplikację identyfikowaną na podstawie jego identyfikatora aplikacji. Zazwyczaj jest to strona, która użytkownik końcowy współdziała z, a żądania tokenów z serwera autoryzacji. Klient musi otrzymać uprawnienia dostępu do zasobu przez właściciela zasobów.
* **Serwer zasobów** jest, w którym znajduje się zasobów lub danych. Relacje zaufania serwera autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth i używa elementu nośnego access_tokens zapewnienie może otrzymać dostęp do zasobu.

## <a name="app-registration"></a>Rejestracja aplikacji
Każda aplikacja, która korzysta z punktu końcowego v2.0 musi być zarejestrowany w [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przed mogą współdziałać, za pomocą uwierzytelniania OAuth lub OpenID Connect. Proces rejestracji aplikacji spowoduje zbieranie & przypisać kilka wartości do aplikacji:

* **Identyfikator aplikacji** który unikatowo identyfikuje aplikację
* A **identyfikator URI przekierowania** lub **identyfikator pakietu** który może służyć do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficzne dla scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Punkty końcowe
Po zarejestrowaniu aplikacji komunikuje się z usługą Azure AD, wysyłając żądania do punktu końcowego v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Gdzie `{tenant}` można wykonać jedną z czterech różnych wartości:

| Wartość | Opis |
| --- | --- |
| `common` |Umożliwia użytkownikom z osobistego konta Microsoft i pracy/służbowego konta w usłudze Azure Active Directory, aby zalogować się do aplikacji. |
| `organizations` |Umożliwia tylko użytkownicy z kontami pracy/służbowych z usługi Azure Active Directory, aby zalogować się do aplikacji. |
| `consumers` |Umożliwia tylko użytkownicy z osobistego konta Microsoft (MSA), aby zalogować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` |Umożliwia tylko użytkownicy z kontami pracy/służbowych z określonego dzierżawcę usługi Azure Active Directory, aby zalogować się do aplikacji. Można użyć nazwy domeny przyjazną dzierżawy usługi Azure AD lub identyfikator guid dzierżawy. |

Aby uzyskać więcej informacji na temat interakcji z tymi punktami końcowymi wybierz poniżej typ danej aplikacji.

## <a name="tokens"></a>Tokeny
Implementacja v2.0 OAuth 2.0 i OpenID Connect należy zwiększone użycie tokenów elementu nośnego, łącznie z tokenów elementu nośnego reprezentowane jako tokenów Jwt. Token elementu nośnego jest tokenem zabezpieczającym lekkie, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" jest każda strona, która może ona powodować tokenu. Jeśli strona muszą najpierw zostać uwierzytelnione z usługą Azure AD do odbierania tokenu elementu nośnego, jeśli wymagane kroki nie są brane do zabezpieczania token w transmisji i przechowywania, można przechwycony i używane przez firmę niezamierzone. Chociaż w niektórych tokeny zabezpieczające wbudowany mechanizm uniemożliwia ich użycie przez osoby nieupoważnione, tokenów elementu nośnego nie mają ten mechanizm i musi być transportowane bezpiecznego kanału, takie jak zabezpieczeń warstwy transportu (HTTPS). Jeśli token elementu nośnego są przesyłane bez zabezpieczeń, man-in środkowej ataku można przez złośliwe stronę do uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie po zapisaniu lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Dalsze szczegółowe informacje o różnych typach tokenów używanych w punkcie końcowym v2.0 jest dostępna w [odwołania do tokenu punktu końcowego v2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protokoły
Jeśli wszystko jest gotowe wyświetlić niektóre przykładowe żądania, Rozpoczynanie pracy z jednego z poniższych samouczki. Każda z nich odpowiada scenariusz, w szczególności uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, który jest prawo przepływu można wyewidencjonować [typy aplikacji, można tworzyć za pomocą v2.0](active-directory-v2-flows.md).

* [Tworzenie przenośnych i aplikacji natywnej z protokołem OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
* [Tworzenie sieci Web aplikacje z Open ID Connect](active-directory-v2-protocols-oidc.md)
* [Tworzenie aplikacji jednej strony za pomocą niejawnego przepływu OAuth 2.0](active-directory-v2-protocols-implicit.md)
* [Demony kompilacji lub po stronie procesy serwera przy użyciu poświadczeń klienta OAuth 2.0 przepływu](active-directory-v2-protocols-oauth-client-creds.md)
* [Uzyskiwanie tokenów w składniku Web API z OAuth 2.0 w imieniu z przepływem](active-directory-v2-protocols-oauth-on-behalf-of.md)
