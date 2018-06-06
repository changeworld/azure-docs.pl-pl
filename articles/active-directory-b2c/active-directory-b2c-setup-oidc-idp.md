---
title: Dodawanie OpenID Connect dostawców tożsamości w wbudowanych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Omówienie Przewodnik na temat dodawania dostawcy OpenID Connect w przypadku wbudowanych zasad w usłudze Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e4325a7c87ac9975e819b22536838ec438fa1bcd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709567"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Usługa Azure Active Directory B2C: Dodawanie niestandardowego dostawcy tożsamości OpenID Connect w wbudowanych zasad

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać funkcji w środowisku produkcyjnym.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) to protokół uwierzytelniania, rozszerzający OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników. Większość dostawców tożsamości, które korzystają z tego protokołu, takich jak [usługi Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), są obsługiwane w usłudze Azure AD B2C. W tym artykule opisano sposób dodawania niestandardowych dostawców tożsamości OpenID Connect do wbudowanych zasad.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Konfigurowanie niestandardowego dostawcy tożsamości OpenID Connect

Aby dodać niestandardowe OpenID Connect dostawcy tożsamości:

1. Wykonaj następujące kroki, aby [przejdź do ustawień usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
1. Polecenie **dostawców tożsamości**.
1. Kliknij pozycję **Dodaj**.
1. Aby uzyskać **typ dostawcy tożsamości**, wybierz pozycję **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Konfigurowanie dostawcy tożsamości OpenID Connect

#### <a name="metadata-url"></a>Adres URL metadanych

Zgodnie z harmonogramem specyfikacji co OpenID Connect dostawców tożsamości opisuje dokument metadanych zawiera większość informacji wymaganych do wykonania logowania. W tym informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. Dokument metadanych OpenID Connect zawsze znajduje się pod adresem punktu końcowego, który kończy się `.well-known\openid-configuration`.

Dla dostawcy tożsamości, OpenID Connect chcesz dodać, wprowadź adres URL metadanych.

#### <a name="client-id-and-secret"></a>Identyfikator klienta i klucz tajny

Aby zezwolić użytkownikom na logowanie, dostawcy tożsamości będzie wymagać od deweloperów do rejestrowania aplikacji w usłudze ich. Ta aplikacja będzie mieć identyfikator (nazywane **identyfikator klienta**) i **klucz tajny klienta**. Skopiuj wartości z dostawcy tożsamości, a następnie wprowadź je do odpowiednich pól.

> [!NOTE]
> Klucz tajny klienta jest opcjonalna. Jednak należy wprowadzić klucz tajny klienta, jeśli chcesz użyć [przepływu kodu autoryzacji](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), który używa klucza tajnego do wymiany kod tokenu.

#### <a name="scope"></a>Zakres

Zakresy określają informacje i uprawnienia, które chcesz zebrać przez dostawcę tożsamości niestandardowej. Żądania protokołu OpenID Connect musi zawierać `openid` zakresu wartości w celu odbierania identyfikator tokenu od dostawcy tożsamości. Bez Identyfikatora tokenu, użytkownicy nie będą mogli zalogować się do usługi Azure AD B2C przy użyciu dostawcy tożsamości niestandardowej.

Inne zakresy można dołączać (oddzielone miejsca). Zapoznaj się z dokumentacją dostawcy tożsamości niestandardowej, aby zobaczyć, jakie inne zakresy mogą być dostępne.

#### <a name="response-type"></a>Typ odpowiedzi

Typ odpowiedzi opisano, jakie informacje będą wysyłane w początkowym wywołaniu `authorization_endpoint` dostawcy tożsamości niestandardowej. 

* `code`: Jak na [przepływu kodu autoryzacji](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), zostanie zwrócony kod, wróć do usługi Azure AD B2C. Usługa Azure AD B2C będzie następnie przejść do wywołania `token_endpoint` wymienić kod tokenu.
* `token`: Token dostępu zostaną zwrócone do usługi Azure AD B2C od dostawcy tożsamości niestandardowej.
* `id_token`: Token identyfikator zostanie zwrócony do usługi Azure AD B2C od dostawcy tożsamości niestandardowej.


#### <a name="response-mode"></a>Tryb odpowiedzi

Tryb odpowiedzi definiuje metodę, które mają być używane do wysyłania danych od dostawcy tożsamości niestandardowej do usługi Azure AD B2C.

* `form_post`: Najlepiej zalecany jest tryb tej odpowiedzi. Odpowiedzi są przesyłane za pośrednictwem protokołu HTTP `POST` metody z kodu lub token jest zakodowany w treści przy użyciu `application/x-www-form-urlencoded` format.
* `query`: Kod lub token zostanie zwrócony jako parametr zapytania.


#### <a name="domain-hint"></a>Wskazówka dotycząca domeny

Wskazówka domeny można przejść bezpośrednio do logowania na stronie dostawcy określonej tożsamości, zamiast użytkownika wykonującego wybór między listy dostawców tożsamości dostępne. Aby zezwolić na tego rodzaju zachowanie, wprowadź wartość dla wskazówki domeny.

Aby przejść do dostawcy tożsamości niestandardowej, Dołącz parametr `domain_hint=<domain hint value>` Twojego żądania podczas wywołania usługi Azure AD B2C w celu logowania się na końcu.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mapowania oświadczenia od dostawcy tożsamości OpenID Connect

Po tożsamość niestandardowa dostawcy wysyła identyfikator tokenu do usługi Azure AD B2C, trzeba usługi Azure AD B2C można zamapować oświadczeń z odebranego tokenu oświadczeń, które usługi Azure AD B2C rozpoznaje i wykorzystuje. 

Dla każdego mapowania poniżej zapoznaj się z dokumentacją dostawcy tożsamości niestandardowej zrozumienie oświadczenia, które są zwracane Wstecz w tokenach dostawcy tożsamości.

* `User ID`: Wprowadź oświadczenie, które zawiera unikatowy identyfikator dla zalogowanego użytkownika.
* `Display Name`: Wprowadź oświadczeń, który udostępnia pełną nazwę użytkownika lub nazwa wyświetlana.
* `Given Name`: Wprowadź oświadczenie, które zawiera imię użytkownika.
* `Surname`: Wprowadź oświadczenie, które zawiera nazwisko użytkownika.
* `Email`: Wprowadź oświadczeń, który udostępnia adres e-mail użytkownika.

## <a name="next-steps"></a>Kolejne kroki

Dodaj niestandardowy OpenID Connect dostawcy tożsamości do Twojej [wbudowanych zasad](active-directory-b2c-reference-policies.md).
