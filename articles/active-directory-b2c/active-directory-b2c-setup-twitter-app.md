---
title: Konfiguracja usługi Twitter dla usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Twitter w aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 6/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dad35f26496306558a6e0105db86321c497a8306
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342973"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Zapewnienie rejestracji i logowania użytkowników z kontami usługi Twitter za pomocą usługi Azure AD B2C

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby korzystać z usługi Twitter jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację usługi Twitter i dostarczyć odpowiednie parametry. Wymagane jest konto usługi Twitter, aby to zrobić. Jeśli nie masz, możesz pobrać na stronie [ https://twitter.com/signup ](https://twitter.com/signup).

1. Przejdź do [aplikacji w usłudze Twitter](https://apps.twitter.com/) i zaloguj się przy użyciu poświadczeń.
2. Kliknij przycisk **Utwórz nową aplikację**.
3. W formularzu, należy podać wartość parametru **nazwa**, **opis**, i **witryny sieci Web**.
4. Aby uzyskać **adresów URL wywołania zwrotnego**, wprowadź `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`. Upewnij się zastąpić **{dzierżawa}** z nazwą (na przykład contosob2c.onmicrosoft.com) i {policyId} dzierżawy za pomocą identyfikatora zasad (na przykład b2c_1_policy).  To wywołanie zwrotne adres URL musi być zapisana w same małe litery. Należy dodać adres URL wywołania zwrotnego dla wszystkich zasad, które używają logowania usługi Twitter. Upewnij się, że używasz `b2clogin.com` zamiast ` login.microsoftonline.com` Jeśli używasz go w aplikacji.
5. Pole wyboru, aby zaakceptować **umowy deweloperskich** i kliknij przycisk **tworzenie aplikacji usługi Twitter**.
6. Po utworzeniu aplikacji, wybierz ją z listy wybierz **ustawienia** kartę, a następnie kliknij przycisk **zaktualizuj ustawienia**.
7. Wybierz **klucze i tokeny dostępu** kartę.
8. Skopiuj wartość **konsumenta** i **klucz tajny klienta**. Konieczne będzie, obie z nich, aby skonfigurować usługi Twitter jako dostawcy tożsamości w dzierżawie.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości w dzierżawie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Administrator globalny dzierżawy usługi Azure AD B2C. 
2. Aby przełączyć się do dzierżawy usługi Azure AD B2C, wybierz katalog usługi Azure AD B2C w prawym górnym rogu portalu.
3. Kliknij przycisk **wszystkich usług**, a następnie wybierz pozycję **usługi Azure AD B2C** z listy usług w ramach **bezpieczeństwo i Obsługa tożsamości**.
4. Kliknij przycisk **dostawców tożsamości**.
5. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "Twitter".
6. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **(wersja zapoznawcza) w serwisie Twitter**i kliknij przycisk **OK**.
7. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź Twitter **konsumenta** dla **identyfikator klienta** i Twitter **klucz tajny klienta** dla **klucz tajny klienta**.
8. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Twitter.

## <a name="next-steps"></a>Kolejne kroki

Tworzenie lub edytowanie [wbudowanych zasad](active-directory-b2c-reference-policies.md) i Dodaj usługi Twitter jako dostawcy tożsamości.