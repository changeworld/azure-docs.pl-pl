---
title: Konfigurowanie rejestracji i logowanie się za pomocą OpenID Connect
titleSuffix: Azure AD B2C
description: Skonfiguruj rejestrację i zaloguj się za pomocą dowolnego dostawcy tożsamości OpenID Connect (IdP) w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188260"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą usługi OpenID Connect przy użyciu usługi Azure Active Directory B2C

[OpenID Connect](openid-connect.md) to protokół uwierzytelniania zbudowany na podstawie protokołu OAuth 2.0, który może być używany do bezpiecznego logowania użytkownika. Większość dostawców tożsamości, którzy używają tego protokołu są obsługiwane w usłudze Azure AD B2C. W tym artykule wyjaśniono, jak można dodać niestandardowych dostawców tożsamości OpenID Connect do przepływów użytkowników.

## <a name="add-the-identity-provider"></a>Dodawanie dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, klikając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję Nowy dostawca **OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Konfigurowanie dostawcy tożsamości

Każdy dostawca tożsamości OpenID Connect opisuje dokument metadanych, który zawiera większość informacji wymaganych do wykonania logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizacja publicznych kluczy podpisywania usługi. Dokument metadanych OpenID Connect zawsze znajduje się `.well-known\openid-configuration`w punkcie końcowym, który kończy się na . W przypadku dostawcy tożsamości OpenID Connect, którego chcesz dodać, wprowadź jego adres URL metadanych.

## <a name="client-id-and-secret"></a>Identyfikator klienta i klucz tajny

Aby umożliwić użytkownikom zalogowanie się, dostawca tożsamości wymaga od deweloperów zarejestrowania aplikacji w ich usłudze. Ta aplikacja ma identyfikator, który jest określany jako **identyfikator klienta** i klucz **tajny klienta**. Skopiuj te wartości z dostawcy tożsamości i wprowadź je do odpowiednich pól.

> [!NOTE]
> Klucz tajny klienta jest opcjonalny. Jednak należy wprowadzić klucz tajny klienta, jeśli chcesz użyć [przepływu kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), który używa klucza tajnego do wymiany kodu dla tokenu.

## <a name="scope"></a>Zakres

Zakres definiuje informacje i uprawnienia, które chcesz zebrać od dostawcy tożsamości niestandardowej. Żądania OpenID Connect `openid` muszą zawierać wartość zakresu, aby odebrać token identyfikatora od dostawcy tożsamości. Bez tokenu identyfikatora użytkownicy nie mogą zalogować się do usługi Azure AD B2C przy użyciu dostawcy tożsamości niestandardowej. Inne zakresy mogą być dołączane oddzielone spacją. Zapoznaj się z dokumentacją dostawcy tożsamości niestandardowej, aby zobaczyć, jakie inne zakresy mogą być dostępne.

## <a name="response-type"></a>Typ odpowiedzi

Typ odpowiedzi opisuje, jakiego rodzaju informacje są wysyłane `authorization_endpoint` z powrotem w początkowym wywołaniu dostawcy tożsamości niestandardowej. Można użyć następujących typów odpowiedzi:

* `code`: Zgodnie z [przepływem kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)kod zostanie zwrócony do usługi Azure AD B2C. Usługa Azure AD B2C `token_endpoint` przechodzi do wywołania do wymiany kodu dla tokenu.
* `id_token`: Token identyfikatora jest zwracany z powrotem do usługi Azure AD B2C od dostawcy tożsamości niestandardowej.

## <a name="response-mode"></a>Tryb odpowiedzi

Tryb odpowiedzi definiuje metodę, która powinna służyć do wysyłania danych z powrotem z dostawcy tożsamości niestandardowej do usługi Azure AD B2C. Można użyć następujących trybów odpowiedzi:

* `form_post`: Ten tryb odpowiedzi jest zalecany dla najlepszego bezpieczeństwa. Odpowiedź jest przesyłana za `POST` pośrednictwem metody HTTP, z kodem lub tokenem zakodowanym w treści przy użyciu formatu. `application/x-www-form-urlencoded`
* `query`: Kod lub token jest zwracany jako parametr kwerendy.

## <a name="domain-hint"></a>Wskazówka domeny

Wskazówki dotyczące domeny mogą służyć do pomijania bezpośrednio do strony logowania określonego dostawcy tożsamości, zamiast podejmować użytkownika wyboru wśród listy dostępnych dostawców tożsamości. Aby zezwolić na tego rodzaju zachowanie, wprowadź wartość wskazówki domeny. Aby przejść do dostawcy tożsamości niestandardowej, dołącz parametr `domain_hint=<domain hint value>` na końcu żądania podczas wywoływania usługi Azure AD B2C dla logowania.

## <a name="claims-mapping"></a>Mapowanie oświadczeń

Po dostawcy tożsamości niestandardowej wysyła token identyfikatora z powrotem do usługi Azure AD B2C, usługi Azure AD B2C musi mieć możliwość mapowania oświadczeń z odebranego tokenu do oświadczeń, które usługa Azure AD B2C rozpoznaje i używa. Dla każdego z następujących mapowań, zapoznaj się z dokumentacją dostawcy tożsamości niestandardowej, aby zrozumieć oświadczenia, które są zwracane z powrotem w tokenach dostawcy tożsamości:

* **Identyfikator użytkownika:** Wprowadź oświadczenie, które zawiera *unikatowy identyfikator* zalogowany użytkownik.
* **Nazwa wyświetlana:** Wprowadź oświadczenie, które zawiera *nazwę wyświetlaną* lub *pełną nazwę* użytkownika.
* **Podana nazwa**: Wprowadź oświadczenie, które zawiera *imię* użytkownika.
* **Nazwisko**: Wprowadź oświadczenie, które zawiera *nazwisko* użytkownika.
* **E-mail**: Wprowadź oświadczenie, które zawiera *adres e-mail* użytkownika.
