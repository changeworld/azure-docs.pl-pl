---
title: Konfigurowanie rejestracji i logowania za pomocą konta w serwisie Twitter przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Twitter w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5732293510a75a3c40df5cf3d31978c5ce599791
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720161"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta w serwisie Twitter przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby użyć konta w serwisie Twitter jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta w serwisie Twitter, możesz pobrać na stronie [ https://twitter.com/signup ](https://twitter.com/signup).

1. Zaloguj się do [aplikacji w usłudze Twitter](https://apps.twitter.com/) przy użyciu swoich poświadczeń usługi Twitter.
2. Wybierz **tworzenie aplikacji**.
3. Wprowadź **nazwy aplikacji**, **opis aplikacji**, i **adres URL witryny internetowej**.
4. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy-name/oauth1/authresp` w **adresów URL wywołania zwrotnego**. Zastąp `your-tenant-name` nazwą dzierżawy i `your-policy-name` o nazwie zasady. Na przykład `b2c_1_signupsignin`. Należy używać małych liter, podczas wprowadzania nazwą dzierżawy, a nazwa zasad, nawet jeśli zostały zdefiniowane przy użyciu wielkich liter w usłudze Azure AD B2C.
5. Zgodę na **umowy deweloperskich** i wybierz **Utwórz**.
7. Wybierz **klucze i tokeny dostępu** kartę.
8. Skopiuj wartości z **klucz interfejsu API** i **klucz tajny interfejsu API**. Należy dysponować go o skonfigurowanie konta w serwisie Twitter jako dostawcy tożsamości w dzierżawie.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości w dzierżawie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.  

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *Twitter*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Twitter**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź klucz interfejsu API dla **identyfikator klienta** oraz klucz tajny interfejsu API dla **klucz tajny klienta**.
8. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Twitter.