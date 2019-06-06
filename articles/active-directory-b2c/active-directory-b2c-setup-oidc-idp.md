---
title: Konfigurowanie rejestrowania i zaloguj się przy użyciu protokołu OpenID Connect — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konfigurowanie rejestracji i logowania za pomocą protokołu OpenID Connect, za pomocą usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 774716aeb28ca5d9563f38272a59c8e675a11e4b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508390"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą protokołu OpenID Connect, za pomocą usługi Azure Active Directory B2C

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać tej funkcji w środowisku produkcyjnym.


[OpenID Connect](active-directory-b2c-reference-oidc.md) to protokół uwierzytelniania korzystających z protokołu OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników. Większość dostawców tożsamości, które używają tego protokołu, są obsługiwane w usłudze Azure AD B2C. W tym artykule opisano sposób dodawania niestandardowych dostawców tożsamości OpenID Connect na temat przepływów użytkownika.

## <a name="add-the-identity-provider"></a>Dodaj dostawcę tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie kliknij przycisk **Dodaj**.
5. Aby uzyskać **typ dostawcy tożsamości**, wybierz opcję **OpenID Connect (wersja zapoznawcza)** .

## <a name="configure-the-identity-provider"></a>Konfigurowanie dostawcy tożsamości

Każdego dostawcy tożsamości z OpenID Connect w tym artykule opisano dokument metadanych, który zawiera większość informacji wymaganych do wykonania logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. Dokument metadanych OpenID Connect zawsze znajduje się w punkcie końcowym, który kończy się `.well-known\openid-configuration`. Dostawca tożsamości OpenID Connect chcesz dodać, wprowadź jego adres URL metadanych.

Aby zezwolić użytkownikom na logowanie, dostawca tożsamości wymaga deweloperów zarejestrować aplikację w ich usługi. Ta aplikacja ma identyfikator, który jest określany jako **identyfikator klienta** i **klucz tajny klienta**. Skopiuj te wartości z dostawcy tożsamości, a następnie wprowadź je do odpowiednich pól.

> [!NOTE]
> Klucz tajny klienta jest opcjonalne. Jednak należy wprowadzić klucz tajny klienta, jeśli chcesz użyć [przepływ kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), który używa klucza tajnego do programu exchange kodu dla tokenu.

Zakres definiuje informacje i uprawnienia, które chcesz zbierać z dostawcą tożsamości niestandardowej. OpenID Connect żądania muszą zawierać `openid` zakres wartości, aby otrzymać identyfikator tokenu od dostawcy tożsamości. Bez tokenu Identyfikacyjnego użytkownicy nie będą mogli zalogować się do usługi Azure AD B2C przy użyciu niestandardowego dostawcy tożsamości. Można dołączyć inne zakresy rozdzielone spacjami. Zajrzyj do dokumentacji dostawcy tożsamości niestandardowej, aby zobaczyć, jakie inne zakresy mogą być dostępne.

Typ odpowiedzi opisano, jakie informacje są wysyłane w początkowe wywołanie `authorization_endpoint` z niestandardowego dostawcy tożsamości. Następujące typy odpowiedzi mogą być używane:

- `code`: Zgodnie [przepływ kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), zostanie zwrócony kod, wróć do usługi Azure AD B2C. Usługa Azure AD B2C będzie kontynuowane do wywołania `token_endpoint` wymianę kodu dla tokenu.
- `token`: Token dostępu jest zwracany do usługi Azure AD B2C z niestandardowego dostawcy tożsamości.
- `id_token`: Identyfikator tokenu jest zwracany do usługi Azure AD B2C z niestandardowego dostawcy tożsamości.

Tryb odpowiedzi definiuje metodę, które mają być używane do wysyłania danych z niestandardowego dostawcy tożsamości do usługi Azure AD B2C. Można użyć następujących trybów odpowiedzi:

- `form_post`: Ten tryb odpowiedzi zaleca się najlepiej. Odpowiedzi są przesyłane za pośrednictwem protokołu HTTP `POST` metoda, za pomocą kodu lub token jest zakodowany w treści, używając `application/x-www-form-urlencoded` formatu.
- `query`: Kod lub token jest zwracany jako parametr zapytania.

Wskazówka dotycząca domeny, można przejść bezpośrednio do strony dostawcy określonej tożsamości, zamiast użytkownika wykonującego wyboru między listy dostawców tożsamości dostępnych logowania. Aby zezwolić na tego rodzaju zachowanie, wprowadź wskazówkę dotyczącą domeny. Aby przejść do niestandardowego dostawcy tożsamości, Dołącz parametr `domain_hint=<domain hint value>` na końcu żądania podczas wywoływania usługi Azure AD B2C do logowania.

Po tożsamość niestandardowa dostawcy wysyła identyfikator tokenu do usługi Azure AD B2C, musi mieć możliwość zmapowania oświadczeń otrzymany token oświadczenia, które usługi Azure AD B2C, rozpoznaje i wykorzystuje usługę Azure AD B2C. Dla każdego z następujące mapowania zapoznaj się z dokumentacją dostawcy tożsamości niestandardowe, aby zrozumieć oświadczenia, które są zwracane w tokenach dostawcy tożsamości:

- `User ID`: Wprowadź oświadczenie, które zawiera unikatowy identyfikator dla zalogowanego użytkownika.
- `Display Name`: Wprowadź oświadczenie, które zawiera nazwę wyświetlaną lub pełną nazwę użytkownika.
- `Given Name`: Wprowadź oświadczenie, które zawiera imię użytkownika.
- `Surname`: Wprowadź oświadczenie, który zawiera nazwisko użytkownika.
- `Email`: Wprowadź oświadczenie, które zawiera adres e-mail użytkownika.

