---
title: Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn, za pomocą usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi LinkedIn w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 84b1ad2ecd2c027c7d8a105579059ceb957f41c6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929002"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Utwórz aplikację usługi LinkedIn

Aby użyć konta LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta usługi LinkedIn, możesz pobrać na stronie [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Zaloguj się do [witryny sieci Web deweloperów LinkedIn](https://www.developer.linkedin.com/) przy użyciu poświadczeń konta usługi LinkedIn.
2. Wybierz **Moje aplikacje**, a następnie kliknij przycisk **tworzenia aplikacji**.
3. Wprowadź **nazwy firmy**, **Nazwa aplikacji**, **opis aplikacji**, **Logo aplikacji**, **użycia aplikacji** , **Adres URL witryny internetowej**, **firmowa Poczta E-mail**, i **Telefon służbowy**.
4. Zaakceptuj **LinkedIn API warunki użytkowania** i kliknij przycisk **przesyłania**.
5. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Znajdziesz je w obszarze **klucze uwierzytelniania**. Konieczne będzie, obie z nich, aby skonfigurować usługi LinkedIn jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **autoryzacji adresów URL przekierowania**. Zastąp **{dzierżawa}** nazwą dzierżawy (np. contoso.onmicrosoft.com). Wybierz **Dodaj**, a następnie kliknij przycisk **aktualizacji**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurowanie konta usługi LinkedIn jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-li-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-li-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *LinkedIn*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **LinkedIn**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji konto LinkedIn, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta usługi LinkedIn.

