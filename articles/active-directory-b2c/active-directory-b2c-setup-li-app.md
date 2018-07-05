---
title: Konfiguracja usługi LinkedIn w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników z kontami usługi LinkedIn w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7588711bd1c2a02e2e9a100d2ba182f43e7df488
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446098"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami usługi LinkedIn
## <a name="create-a-linkedin-application"></a>Utwórz aplikację usługi LinkedIn
Aby użyć usługi LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację usługi LinkedIn i dostarczyć odpowiednie parametry. Wymagane jest konto usługi LinkedIn, aby to zrobić. Jeśli nie masz, możesz pobrać na stronie [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Przejdź do [witryny sieci Web deweloperów LinkedIn](https://www.developer.linkedin.com/) i zaloguj się przy użyciu poświadczeń konta usługi LinkedIn.
2. Kliknij przycisk **Moje aplikacje** w górnym pasku menu a następnie kliknij przycisk **tworzenia aplikacji**.
   
    ![LinkedIn — nowej aplikacji](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. W **Utwórz nową aplikację** formularza, wprowadź odpowiednie informacje (**nazwa firmy**, **nazwa**, **opis**, **Adres URL Logo aplikacji**, **użycia aplikacji**, **adres URL witryny internetowej**, **firmowa Poczta E-mail** i **Telefon służbowy**).
4. Zaakceptuj **LinkedIn API warunki użytkowania** i kliknij przycisk **przesyłania**.
   
    ![LinkedIn — Rejestracja aplikacji](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. (Możesz znaleźć je w obszarze **klucze uwierzytelniania**.) Konieczne będzie, obie z nich, aby skonfigurować usługi LinkedIn jako dostawcy tożsamości w dzierżawie.
   
   > [!NOTE]
   > **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
   > 
   > 
6. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **autoryzacji adresów URL przekierowania** pola (w obszarze **OAuth 2.0**). Zastąp **{dzierżawa}** nazwą dzierżawy (np. contoso.onmicrosoft.com). Kliknij przycisk **Dodaj**, a następnie kliknij przycisk **aktualizacji**. **{Dzierżawa}** . wartość powinna być małe litery.
   
    ![LinkedIn — ustawienia aplikacji](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi LinkedIn jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "LI".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **LinkedIn**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta oraz klucz tajny klienta aplikacji usługi LinkedIn, która została utworzona wcześniej.
7. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi LinkedIn.

