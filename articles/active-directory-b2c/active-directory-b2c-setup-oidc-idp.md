---
title: Dodawanie dostawcy OpenID Connect tożsamości w wbudowanych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Omówienie Przewodnik na temat dodawania dostawcy OpenID Connect w wbudowanych zasad w ramach usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5baf95cd2426c9753620cba7c5a67b4c1672788
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444229"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Usługa Azure Active Directory B2C: Dodaj niestandardowego dostawcę tożsamości OpenID Connect w wbudowane zasady

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać tej funkcji w środowisku produkcyjnym.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) to protokół uwierzytelniania korzystających z protokołu OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników. Większość dostawców tożsamości, które używają tego protokołu, takich jak [usługi Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), są obsługiwane w usłudze Azure AD B2C. W tym artykule opisano sposób dodawania niestandardowych dostawców tożsamości OpenID Connect do wbudowanych zasad.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Konfigurowanie niestandardowego dostawcy tożsamości OpenID Connect

Aby dodać niestandardowe OpenID Connect dostawcy tożsamości:

1. Wykonaj następujące kroki, aby [Przechodzenie do ustawień usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
1. Kliknij pozycję **dostawców tożsamości**.
1. Kliknij pozycję **Dodaj**.
1. Aby uzyskać **typ dostawcy tożsamości**, wybierz opcję **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Konfigurowanie dostawcy tożsamości OpenID Connect

#### <a name="metadata-url"></a>Adres URL metadanych

Zgodnie z specyfikacją, co OpenID Connect dostawców tożsamości w tym artykule opisano dokument metadanych, który zawiera większość informacji wymaganych do wykonania logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. Dokument metadanych OpenID Connect zawsze znajduje się w punkcie końcowym, który kończy się `.well-known\openid-configuration`.

Dostawca tożsamości OpenID Connect chcesz dodać, wprowadź jego adres URL metadanych.

#### <a name="client-id-and-secret"></a>Identyfikator klienta i klucz tajny

Aby zezwolić użytkownikom na logowanie, dostawca tożsamości wymaga deweloperów zarejestrować aplikację w ich usługi. Ta aplikacja będzie miała Identyfikatora (nazywane **identyfikator klienta**) i **klucz tajny klienta**. Skopiuj te wartości z dostawcy tożsamości, a następnie wprowadź je do odpowiednich pól.

> [!NOTE]
> Klucz tajny klienta jest opcjonalne. Jednak należy wprowadzić klucz tajny klienta, jeśli chcesz użyć [przepływ kodu autoryzacji](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), który używa klucza tajnego do programu exchange kodu dla tokenu.

#### <a name="scope"></a>Zakres

Zakresy określają informacje i uprawnienia, które chcesz zbierać z dostawcą tożsamości niestandardowej. OpenID Connect żądania muszą zawierać `openid` zakres wartości, aby otrzymać identyfikator tokenu od dostawcy tożsamości. Bez tokenu Identyfikacyjnego użytkownicy nie będą mogli zalogować się do usługi Azure AD B2C przy użyciu niestandardowego dostawcy tożsamości.

Można dołączyć inne zakresy (oddzielone miejsca). Zajrzyj do dokumentacji dostawcy tożsamości niestandardowej, aby zobaczyć, jakie inne zakresy mogą być dostępne.

#### <a name="response-type"></a>Typ odpowiedzi

Typ odpowiedzi opisano, jakie informacje będą wysyłane w początkowe wywołanie `authorization_endpoint` z niestandardowego dostawcy tożsamości. 

* `code`: Zgodnie [przepływ kodu autoryzacji](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), zostanie zwrócony kod, wróć do usługi Azure AD B2C. Usługa Azure AD B2C będzie następnie przejść do wywołania `token_endpoint` wymianę kodu dla tokenu.
* `token`: Token dostępu zostanie zwrócony do usługi Azure AD B2C z niestandardowego dostawcy tożsamości.
* `id_token`: Identyfikator tokenu zostaną zwrócone do usługi Azure AD B2C z niestandardowego dostawcy tożsamości.


#### <a name="response-mode"></a>Tryb odpowiedzi

Tryb odpowiedzi definiuje metodę, które mają być używane do wysyłania danych z niestandardowego dostawcy tożsamości do usługi Azure AD B2C.

* `form_post`: Ten tryb odpowiedzi zaleca się najlepiej. Odpowiedzi są przesyłane za pośrednictwem protokołu HTTP `POST` metoda, za pomocą kodu lub token jest zakodowany w treści, używając `application/x-www-form-urlencoded` formatu.
* `query`: Ten kod lub token zostanie zwrócony jako parametr zapytania.


#### <a name="domain-hint"></a>Wskazówka dotycząca domeny

Wskazówka dotycząca domeny, można przejść bezpośrednio do strony dostawcy określonej tożsamości, zamiast użytkownika wykonującego wyboru między listy dostawców tożsamości dostępnych logowania. Aby zezwolić na tego rodzaju zachowanie, wprowadź wskazówkę dotyczącą domeny.

Aby przejść do niestandardowego dostawcy tożsamości, Dołącz parametr `domain_hint=<domain hint value>` na końcu żądania podczas wywoływania usługi Azure AD B2C do logowania.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mapowanie oświadczeń od dostawcy tożsamości OpenID Connect

Po tożsamość niestandardowa dostawcy wysyła identyfikator tokenu do usługi Azure AD B2C, musi mieć możliwość zmapowania oświadczeń otrzymany token oświadczenia, które usługi Azure AD B2C, rozpoznaje i wykorzystuje usługę Azure AD B2C. 

Dla każdego z poniższych mapowania zapoznaj się z dokumentacją niestandardowego dostawcy tożsamości do zrozumienia oświadczenia, które są zwracane w tokenach dostawcy tożsamości.

* `User ID`: Wprowadź oświadczenia, który zawiera unikatowy identyfikator dla zalogowanego użytkownika.
* `Display Name`: Wprowadź oświadczenia, który zawiera nazwę wyświetlaną lub pełną nazwę użytkownika.
* `Given Name`: Wprowadź oświadczenia, który zawiera imię użytkownika.
* `Surname`: Wprowadź oświadczenia, który zawiera nazwisko użytkownika.
* `Email`: Wprowadź oświadczenia, który zawiera adres e-mail użytkownika.

## <a name="next-steps"></a>Kolejne kroki

Tożsamość dostawcy OpenID Connect niestandardowego do usługi [wbudowanych zasad](active-directory-b2c-reference-policies.md).
