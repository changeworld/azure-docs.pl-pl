---
title: Konfigurowanie rejestracji i logowania za pomocą konta Microsoft, za pomocą usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Microsoft w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 338c2a197cb50091c3b272e0ce590341ffda1d7f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341087"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Microsoft, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft

Aby użyć konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Microsoft, możesz pobrać na stronie [ https://www.live.com/ ](https://www.live.com/).

1. Zaloguj się do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przy użyciu poświadczeń konta Microsoft.
2. W prawym górnym rogu wybierz **Dodaj aplikację**.
3. Podaj **nazwa** aplikacji i kliknij pozycję **Utwórz**.
4. Na stronie rejestracji należy skopiować wartość **identyfikator aplikacji**. Umożliwia ona skonfiguruj swoje konto Microsoft jako dostawcy tożsamości w dzierżawie.
5. Wybierz **platformy Dodaj**, a następnie i wybierz polecenie **Web**.
6. Wprowadź `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` w **przekierowania adresów URL**. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c).
7. Wybierz **wygenerować nowego hasła** w obszarze **wpisów tajnych aplikacji**. Skopiuj nowe hasło, które są wyświetlane na ekranie. Możesz go potrzebować, aby skonfigurować konto Microsoft jako dostawcy tożsamości w dzierżawie. To hasło jest ważnym poświadczeniem zabezpieczeń.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurowanie konta Microsoft jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *MSA*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Account Microsoft**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator aplikacji, które wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź hasło, które są rejestrowane jako **klucz tajny klienta**aplikacji konta Microsoft, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta Microsoft.

