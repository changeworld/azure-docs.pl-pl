---
title: Konfiguracja konta Microsoft w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników z kontami Microsoft w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c788b14a99125a208390cd4f8ead338efed06933
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444171"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami Microsoft
## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft
Aby użyć konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację konta Microsoft i dostarczyć odpowiednie parametry. Wymagane jest konto Microsoft, aby to zrobić. Jeśli nie masz, możesz pobrać na stronie [ https://www.live.com/ ](https://www.live.com/).

1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) i zaloguj się przy użyciu poświadczeń konta Microsoft.
2. Kliknij przycisk **Dodaj aplikację**.
   
    ![Microsoft konta — Dodaj nową aplikację](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Podaj **nazwa** aplikacji i kliknij pozycję **tworzenie aplikacji**.
   
    ![Konto Microsoft — Nazwa aplikacji](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Skopiuj wartość **identyfikator aplikacji**. Należy skonfigurować konto Microsoft jako dostawcy tożsamości w dzierżawie.
   
    ![Konto Microsoft — identyfikator aplikacji](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Kliknij pozycję **platformy Dodaj** i wybierz polecenie **Web**.
   
    ![Microsoft konta — Dodaj platformę](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Konto Microsoft — w sieci Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **identyfikatory URI przekierowań** pola. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com).
   
    ![Konto Microsoft — adres URL przekierowania](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Kliknij pozycję **wygenerować nowe hasło** w obszarze **wpisów tajnych aplikacji** sekcji. Skopiuj nowe hasło, które są wyświetlane na ekranie. Należy skonfigurować konto Microsoft jako dostawcy tożsamości w dzierżawie. To hasło jest ważnym poświadczeniem zabezpieczeń.
   
    ![Microsoft konta — Generowanie nowego hasła](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Konto Microsoft — nowe hasło](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Zaznacz pole wyboru, które mówi **Obsługa zestawu Live SDK** w obszarze **zaawansowane opcje** sekcji. Kliknij pozycję **Zapisz**.
   
    ![Konto Microsoft — Obsługa zestawu Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie konta Microsoft jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "MSA".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **konta Microsoft**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator aplikacji oraz hasła aplikacji konta Microsoft, która została utworzona wcześniej.
7. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta Microsoft.

