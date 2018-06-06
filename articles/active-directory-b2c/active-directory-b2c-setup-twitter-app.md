---
title: Konfiguracja usługi Twitter dla usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Podaj rejestracji i logowania do klientów z kontami usługi Twitter w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68b9e9edbacda08bc98b6b7af5d944d3df9edca1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709584"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Umożliwiają rejestracji i logowania użytkowników z kontami usługi Twitter, za pomocą usługi Azure AD B2C

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter
Aby użyć usługi Twitter jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację usługi Twitter i dostarczyć prawo parametrów. Należy to zrobić przy użyciu konta usługi Twitter. Jeśli nie masz, możesz pobrać go w [ https://twitter.com/signup ](https://twitter.com/signup).

1. Przejdź do [aplikacji w usłudze Twitter](https://apps.twitter.com/) i zaloguj się przy użyciu poświadczeń.
2. Kliknij przycisk **Utwórz nową aplikację**. 
3. W formularzu, należy podać wartość parametru **nazwa**, **opis**, i **witryny sieci Web**.
4. Aby uzyskać **wywołania zwrotnego adresu URL**, wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Upewnij się zastąpić **{dzierżawa}** nazwą Twojej dzierżawy (na przykład contosob2c.onmicrosoft.com).
5. Pole wyboru, aby zaakceptować **umowy Developer** i kliknij przycisk **tworzenie aplikacji Twitter**.
6. Po utworzeniu aplikacji, wybierz z listy, a następnie wybierz **ustawienia** kartę.
7. Wyczyść **włączyć blokowania wywołania zwrotnego** , a następnie kliknij przycisk **zaktualizować ustawienia**.
8. Wybierz **kluczy i tokenów dostępu** kartę.
9. Skopiuj wartość **konsumenta** i **klucz tajny klienta**. Należy obie z nich, aby skonfigurować funkcję dostawcy tożsamości w dzierżawie usługi Twitter.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Skonfiguruj funkcję dostawcy tożsamości w dzierżawie usługi Twitter
1. Zaloguj się do [portalu Azure](https://portal.azure.com/) jako Administrator globalny dzierżawy usługi Azure AD B2C. 
2. Aby przełączyć się do dzierżawy usługi Azure AD B2C, wybierz katalog usługi Azure AD B2C w prawym górnym rogu portalu.
3. Kliknij przycisk **wszystkie usługi**, a następnie wybierz **usługi Azure AD B2C** z listy usług w obszarze **bezpieczeństwo i Obsługa tożsamości**.
4. Kliknij przycisk **dostawców tożsamości**.
4. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "Twitter".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **(wersja zapoznawcza) w serwisie Twitter**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfigurować ten dostawca tożsamości** , a następnie wprowadź Twitter **konsumenta** dla **identyfikator klienta** i Twitter **klucz tajny klienta** dla **klucz tajny klienta**.
7. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Twitter.

## <a name="next-steps"></a>Kolejne kroki

Utwórz lub Edytuj [wbudowanych zasad](active-directory-b2c-reference-policies.md) i dodać funkcję dostawcy tożsamości usługi Twitter.