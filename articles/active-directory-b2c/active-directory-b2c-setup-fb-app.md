---
title: Konfigurowanie rejestracji i logowania za pomocą konta Facebook, za pomocą usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi Facebook w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: de059e3875b5f15526cb176d43a019fd2d9ee9b9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901385"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Facebook, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Tworzenie aplikacji usługi Facebook

Aby użyć konta w serwisie Facebook jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz pobrać na stronie [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Zaloguj się do [serwisu Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta serwisu Facebook.
2. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper usługi Facebook. Aby to zrobić, wybierz **zarejestrować** w prawym górnym rogu strony, Zaakceptuj zasady w serwisie Facebook, a kroki rejestracji.
3. Wybierz **Moje aplikacje** a następnie kliknij przycisk **Dodaj nową aplikację**. 
4. Wprowadź **nazwę wyświetlaną** i prawidłową **E-mail kontaktowy**.
5. Kliknij przycisk **utworzyć identyfikator aplikacji**. Może to wymagać zaakceptować zasady platformy usługi Facebook i ukończyć sprawdzania zabezpieczeń w trybie online.
6. Wybierz **ustawienia** > **podstawowe**.
7. W dolnej części strony wybierz **Dodaj platformy**, a następnie wybierz pozycję **witryny sieci Web**.
8. Wprowadź `https://login.microsoftonline.com/` w **adres URL witryny**. Wprowadź adres URL dla **adres URL zasad zachowania poufności**, na przykład `http://www.contoso.com`.
9. Wybierz **Zapisz zmiany**.
11. W górnej części strony, skopiuj wartość **Identyfikatora aplikacji**. 
12. Kliknij przycisk **Pokaż** i skopiuj wartość **klucz tajny aplikacji**. Obie z nich służy do konfigurowania usługi Facebook jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
13. Wybierz **produktów**, a następnie wybierz pozycję **Konfigurowanie** w obszarze **logowania do usługi Facebook**.
14. Wybierz **ustawienia** w obszarze **logowania do usługi Facebook**.
15. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **identyfikatory URI przekierowania OAuth prawidłowe** . Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com). Kliknij przycisk **Zapisz zmiany** w dolnej części strony.
16. Aby udostępnić aplikację usługi Facebook do usługi Azure AD B2C wybierz **przeglądu aplikacji**ustaw **publicznego wprowadzić Moja aplikacja?** do **tak**, wybierz kategorię, na przykład `Business and Pages`a następnie kliknij przycisk **Potwierdź**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta usługi Facebook jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Facebook*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Facebook**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz tajny aplikacji, które są rejestrowane jako **klucz tajny klienta** programu Facebook aplikacja, która została utworzona wcześniej).
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Facebook.