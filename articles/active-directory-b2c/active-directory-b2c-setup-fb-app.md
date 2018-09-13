---
title: Konfigurowanie rejestracji i logowania za pomocą konta Facebook, za pomocą usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi Facebook w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68016f9bc2c6be9bdc0ac06b454563b6ae135d38
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714857"
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
7. Wybierz **kategorii**, na przykład `Business and Pages`. Ta wartość jest wymagana przez usługi Facebook, ale nie są używane dla usługi Azure AD B2C.
8. W dolnej części strony wybierz **Dodaj platformy**, a następnie wybierz pozycję **witryny sieci Web**.
9. W **adres URL witryny**, wprowadź `https://your-tenant-name.b2clogin.com/` zastępowanie `your-tenant-name` nazwą Twojej dzierżawy. Wprowadź adres URL dla **adres URL zasad zachowania poufności**, na przykład `http://www.contoso.com`. Adres URL zasad jest stroną, których obsługa zapewniające informacje o ochronie prywatności dla aplikacji.
10. Wybierz **Zapisz zmiany**.
11. W górnej części strony, skopiuj wartość **Identyfikatora aplikacji**. 
12. Kliknij przycisk **Pokaż** i skopiuj wartość **klucz tajny aplikacji**. Obie z nich służy do konfigurowania usługi Facebook jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
13. Wybierz **produktów**, a następnie wybierz pozycję **Konfigurowanie** w obszarze **logowania do usługi Facebook**.
14. Wybierz **ustawienia** w obszarze **logowania do usługi Facebook**.
15. W **identyfikatory URI przekierowania OAuth prawidłowe**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Kliknij przycisk **Zapisz zmiany** w dolnej części strony.
16. Aby udostępnić aplikację usługi Facebook do usługi Azure AD B2C wybierz **przeglądu aplikacji**ustaw **publicznego wprowadzić Moja aplikacja?** do **tak**, a następnie kliknij przycisk **Potwierdź** .

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta usługi Facebook jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Facebook*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Facebook**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz tajny aplikacji, które są rejestrowane jako **klucz tajny klienta** programu Aplikacja usługi Facebook, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Facebook.