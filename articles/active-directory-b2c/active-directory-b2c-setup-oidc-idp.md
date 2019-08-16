---
title: Konfigurowanie rejestracji i logowania za pomocą OpenID Connect Connect-Azure Active Directory B2C
description: Skonfiguruj konto i zaloguj się za pomocą usługi OpenID Connect Connect przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 720deb28ce124af23035337ac88cfb1d37fc7c53
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509684"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą usługi OpenID Connect Connect przy użyciu Azure Active Directory B2C

[OpenID Connect Connect](active-directory-b2c-reference-oidc.md) to protokół uwierzytelniania oparty na protokole OAuth 2,0, który może służyć do bezpiecznego logowania użytkowników. Większość dostawców tożsamości korzystających z tego protokołu jest obsługiwana w Azure AD B2C. W tym artykule wyjaśniono, jak dodać niestandardowych dostawców tożsamości OpenID Connect Connect do przepływów użytkownika.

## <a name="add-the-identity-provider"></a>Dodawanie dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **dostawcy tożsamości**, a następnie kliknij przycisk **Dodaj**.
5. W polu **Typ dostawcy tożsamości**wybierz pozycję **OpenID Connect Połącz**.

## <a name="configure-the-identity-provider"></a>Konfigurowanie dostawcy tożsamości

Każdy dostawca tożsamości OpenID Connect Connect zawiera opis dokumentu metadanych, który zawiera większość informacji wymaganych do przeprowadzenia logowania. Obejmuje to takie informacje, jak adresy URL, które mają być używane, oraz lokalizację publicznych kluczy podpisywania usługi. Dokument metadanych OpenID Connect Connect zawsze znajduje się w punkcie końcowym, który się `.well-known\openid-configuration`skończy. Dla dostawcy tożsamości OpenID Connect Connect, który chcesz dodać, wprowadź adres URL metadanych.

Aby umożliwić użytkownikom logowanie się, dostawca tożsamości wymaga od deweloperów zarejestrowania aplikacji w swojej usłudze. Ta aplikacja ma identyfikator, który jest określany jako **Identyfikator klienta** i **klucz tajny klienta**. Skopiuj te wartości z dostawcy tożsamości i wprowadź je do odpowiednich pól.

> [!NOTE]
> Wpis tajny klienta jest opcjonalny. Należy jednak wprowadzić klucz tajny klienta, jeśli chcesz użyć [przepływu kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), który używa klucza tajnego do wymiany kodu dla tokenu.

Zakres definiuje informacje i uprawnienia, które chcesz zebrać od niestandardowego dostawcy tożsamości. Żądania połączenia OpenID Connect muszą zawierać `openid` wartość zakresu w celu uzyskania tokenu identyfikatora od dostawcy tożsamości. Bez tokenu identyfikatora użytkownicy nie mogą zalogować się do Azure AD B2C przy użyciu niestandardowego dostawcy tożsamości. Inne zakresy można dołączać rozdzielone spacjami. Zapoznaj się z dokumentacją niestandardowego dostawcy tożsamości, aby zobaczyć, jakie inne zakresy mogą być dostępne.

Typ odpowiedzi opisuje, jakiego rodzaju informacje są wysyłane z powrotem podczas początkowego wywołania do `authorization_endpoint` niestandardowego dostawcy tożsamości. Można użyć następujących typów odpowiedzi:

- `code`: Zgodnie z [przepływem kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)kod zostanie zwrócony z powrotem do Azure AD B2C. Azure AD B2C przechodzi do wywołania `token_endpoint` programu w celu wymiany kodu dla tokenu.
- `token`: Token dostępu jest zwracany z powrotem do Azure AD B2C z niestandardowego dostawcy tożsamości.
- `id_token`: Token identyfikatora jest zwracany z powrotem do Azure AD B2C z niestandardowego dostawcy tożsamości.

Tryb odpowiedzi definiuje metodę, która powinna być używana do wysyłania danych z powrotem z niestandardowego dostawcy tożsamości do Azure AD B2C. Można używać następujących trybów odpowiedzi:

- `form_post`: Ten tryb odpowiedzi jest zalecany w celu uzyskania najlepszego zabezpieczenia. Odpowiedź jest przesyłana przez metodę http `POST` , z kodem lub tokenem zakodowanym w treści `application/x-www-form-urlencoded` przy użyciu formatu.
- `query`: Kod lub token jest zwracany jako parametr zapytania.

Wskazówki dotyczącej domeny można użyć, aby pominąć bezpośrednio na stronie logowania określonego dostawcy tożsamości, zamiast wybierać użytkowników z listy dostępnych dostawców tożsamości. Aby zezwolić na tego rodzaju zachowanie, wprowadź wartość dla wskazówki dotyczącej domeny. Aby przejść do niestandardowego dostawcy tożsamości, należy dołączyć parametr `domain_hint=<domain hint value>` do końca żądania podczas wywoływania Azure AD B2C logowania.

Gdy niestandardowy dostawca tożsamości wyśle token identyfikatora z powrotem do Azure AD B2C, Azure AD B2C musi mieć możliwość mapowania oświadczeń od otrzymanego tokenu do oświadczeń, które Azure AD B2C rozpoznawane i używane. Dla każdego z poniższych mapowań zapoznaj się z dokumentacją niestandardowego dostawcy tożsamości, aby zrozumieć oświadczenia, które są zwracane z powrotem do tokenów dostawcy tożsamości:

- `User ID`: Wprowadź wartość, która zapewnia unikatowy identyfikator zalogowanego użytkownika.
- `Display Name`: Wprowadź wartość, która zawiera nazwę wyświetlaną lub pełną nazwę użytkownika.
- `Given Name`: Wprowadź wartość, która zawiera imię użytkownika.
- `Surname`: Wprowadź wartość, która zawiera nazwisko użytkownika.
- `Email`: Wprowadź wartość, która zawiera adres e-mail użytkownika.

