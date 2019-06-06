---
title: Konfigurowanie rejestracji i logowania za pomocą konta w serwisie Twitter przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Twitter w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 706807039ac7c13cd88bc8ac99acf0d7ece3b384
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508160"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta w serwisie Twitter przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby korzystać z usługi Twitter jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć aplikację usługi Twitter. Jeśli nie masz jeszcze konta w serwisie Twitter, możesz pobrać na stronie [ https://twitter.com/signup ](https://twitter.com/signup).

1. Zaloguj się do [deweloperów w usłudze Twitter](https://developer.twitter.com/en/apps) witryny sieci Web przy użyciu poświadczeń konta usługi Twitter.
2. Wybierz **tworzenie aplikacji**.
3. Wprowadź **nazwy aplikacji** i **opis aplikacji**.
4. W **adres URL witryny internetowej**, wprowadź `https://your-tenant.b2clogin.com`. Zastąp `your-tenant` nazwą Twojej dzierżawy. Na przykład https://contosob2c.b2clogin.com.
5. Aby uzyskać **adresów URL wywołania zwrotnego**, wprowadź `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Zastąp `your-tenant` o nazwie Nazwa dzierżawy i `your-user-flow-Id` z identyfikatorem przepływ użytkownika. Na przykład `b2c_1A_signup_signin_twitter`. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C.
6. W dolnej części strony, przeczytaj i zaakceptuj warunki, a następnie wybierz **Utwórz**.
7. Na **szczegóły aplikacji** wybierz opcję **Edytuj > Edytuj szczegóły**, pole wyboru dla **Włącz Zaloguj się przy użyciu usługi Twitter**, a następnie wybierz pozycję **Zapisz**.
8. Wybierz **klucze i tokeny** i Zarejestruj **klucz interfejsu API klienta** i **klucz tajny konsumenta interfejsu API** wartości do użycia później.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości w dzierżawie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *Twitter*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Twitter**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź klucz interfejsu API dla **identyfikator klienta** oraz klucz tajny interfejsu API dla **klucz tajny klienta**.
8. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Twitter.
