---
title: Konfigurowanie rejestracji i logowania za pomocą konta Google przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont Google w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 477bd6047da639dcf21592a7ec0c1b80844e031e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337738"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Google przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-google-application"></a>Tworzenie aplikacji Google

Aby użyć konta Google jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Google możesz pobrać na stronie [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Zaloguj się do [konsoli deweloperów Google](https://console.developers.google.com/) przy użyciu poświadczeń konta Google.
2. Wybierz **Tworzenie projektu**, a następnie kliknij przycisk **Utwórz**. Jeśli utworzono projekty przed wybierz listę projektów, a następnie wybierz **nowy projekt**.
3. Wprowadź **Nazwa projektu**, a następnie kliknij przycisk **Utwórz**.
3. Wybierz **poświadczenia** w menu po lewej stronie, a następnie wybierz **Utwórz poświadczenia** > **identyfikator klienta Oauth**.
4. Wybierz **ekranie wyrażania zgody Konfiguruj**.
5. Wybierz lub Określ prawidłową **adres E-mail**, podaj **nazwa produktu pokazywana użytkownikom**i kliknij przycisk **Zapisz**.
6. W obszarze **typ aplikacji**, wybierz opcję **aplikacji sieci Web**.
7. Wprowadź **nazwa** dla aplikacji, wprowadź `https://{tenant}.b2clogin.com` w **JavaScript autoryzowanych źródeł**, i `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` w **identyfikatory URI przekierowania autoryzowanych**. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c).
8. Kliknij pozycję **Utwórz**.
9. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Konieczne będzie, obie z nich, aby skonfigurować Google jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Skonfiguruj konto Google jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Google*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Google**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji Google, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Google.

