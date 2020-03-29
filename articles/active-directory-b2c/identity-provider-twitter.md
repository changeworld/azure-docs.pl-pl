---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta na Twitterze
titleSuffix: Azure AD B2C
description: Zapewnij rejestrację i zaloguj się do klientów za pomocą kont Twitter w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051463"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta twitterowego przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby używać twittera jako dostawcy tożsamości w usłudze Azure AD B2C, należy utworzyć aplikację Twitter. Jeśli nie masz jeszcze konta na Twitterze, [https://twitter.com/signup](https://twitter.com/signup)możesz zarejestrować się w pliku .

1. Zaloguj się w witrynie [Twitter Developers](https://developer.twitter.com/en/apps) za pomocą poświadczeń konta na Twitterze.
1. Wybierz **pozycję Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji** i **opis aplikacji**.
1. W **adresie URL witryny**wprowadź `https://your-tenant.b2clogin.com`. Zamień `your-tenant` na nazwę dzierżawy. Na przykład `https://contosob2c.b2clogin.com`.
1. Aby uzyskać adres URL `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` **wywołania zwrotnego,** wprowadź . Zastąp `your-tenant` nazwą nazwy `your-user-flow-Id` dzierżawy i identyfikatorem przepływu użytkownika. Na przykład `b2c_1A_signup_signin_twitter`. Podczas wprowadzania nazwy dzierżawy i identyfikatora przepływu użytkownika należy używać wszystkich małych liter, nawet jeśli są one zdefiniowane za pomocą wielkich liter w usłudze Azure AD B2C.
1. U dołu strony przeczytaj i zaakceptuj warunki, a następnie wybierz pozycję **Utwórz**.
1. Na stronie **Szczegóły aplikacji** wybierz pozycję Edytuj > **Edytuj szczegóły**, zaznacz pole wyboru Włącz zaloguj się za **pomocą twittera,** a następnie wybierz pozycję **Zapisz**.
1. Wybierz **klucze i tokeny** i zarejestruj **klucz interfejsu API konsumenta** i tajne wartości **klucza interfejsu API konsumenta, które** mają być używane później.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie twittera jako dostawcy tożsamości w dzierżawie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję **Twitter**.
1. Wprowadź **nazwę**. Na przykład *Twitter*.
1. W przypadku identyfikatora **klienta**wprowadź klucz interfejsu API konsumenta aplikacji Twitter, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź zarejestrowany klucz tajny interfejsu API konsumenta.
1. Wybierz **pozycję Zapisz**.
