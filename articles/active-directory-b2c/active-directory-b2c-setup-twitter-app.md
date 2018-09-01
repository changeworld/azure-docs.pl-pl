---
title: Konfigurowanie rejestracji i logowania za pomocą konta w serwisie Twitter przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Twitter w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6d8e9245e95c08aad69cd05f338b6260e554469b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337794"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta w serwisie Twitter przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby użyć konta w serwisie Twitter jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta w serwisie Twitter, możesz pobrać na stronie [ https://twitter.com/signup ](https://twitter.com/signup).

1. Zaloguj się do [aplikacji w usłudze Twitter](https://apps.twitter.com/) przy użyciu swoich poświadczeń usługi Twitter.
2. Wybierz **Utwórz nową aplikację**.
3. Wprowadź **nazwa**, **opis**, i **witryny sieci Web**.
4. Wprowadź `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp` w **adresów URL wywołania zwrotnego**. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c) i **{policyId}** za pomocą Identyfikatora zasad (na przykład b2c_1_policy). Należy dodać adres URL wywołania zwrotnego dla wszystkich zasad, które używają konta w serwisie Twitter. 
5. Zgodę na **umowy deweloperskich** i wybierz **tworzenie aplikacji usługi Twitter**.
7. Wybierz **klucze i tokeny dostępu** kartę.
8. Skopiuj wartość **konsumenta** i **klucz tajny klienta**. Należy dysponować go o skonfigurowanie konta w serwisie Twitter jako dostawcy tożsamości w dzierżawie.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości w dzierżawie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *Twitter*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Twitter**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź klucz klienta dla **identyfikator klienta** i **klucz tajny klienta** dla **klucz tajny klienta**.
8. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Twitter.