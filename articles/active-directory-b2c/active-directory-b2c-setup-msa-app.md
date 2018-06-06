---
title: Konfiguracja konta Microsoft w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przedstawić rejestracji i logowania użytkowników z kontami Microsoft w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: be8c691835d2e89d5d305e01f9717e55e3741baf
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712423"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Usługa Azure Active Directory B2C: Umożliwiają rejestracji i logowania użytkowników z kontami Microsoft
## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft
Do używania konta Microsoft jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację konta Microsoft i dostarczyć prawo parametrów. Musisz mieć konto Microsoft, w tym celu. Jeśli nie masz, możesz pobrać go w [ https://www.live.com/ ](https://www.live.com/).

1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) i zaloguj się przy użyciu poświadczeń konta Microsoft.
2. Kliknij przycisk **Dodaj aplikację**.
   
    ![Microsoft konta — Dodawanie nowej aplikacji](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Podaj **nazwa** dla aplikacji i kliknij przycisk **tworzenie aplikacji**.
   
    ![Konto Microsoft — Nazwa aplikacji](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Skopiuj wartość **identyfikator aplikacji**. Należy go skonfigurować konto Microsoft jako dostawca tożsamości w dzierżawie.
   
    ![Konto Microsoft — identyfikator aplikacji](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Polecenie **platformy Dodaj** i wybierz polecenie **Web**.
   
    ![Microsoft konta — Dodawanie platformy](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Konto Microsoft — sieci Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **identyfikator URI przekierowania** pola. Zastąp **{dzierżawa}** nazwą Twojej dzierżawy (na przykład contosob2c.onmicrosoft.com).
   
    ![Konto Microsoft — adres URL przekierowania](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Polecenie **wygenerować nowe hasło** w obszarze **klucze tajne aplikacji** sekcji. Skopiuj nowe hasło, które są wyświetlane na ekranie. Należy go skonfigurować konto Microsoft jako dostawca tożsamości w dzierżawie. To hasło jest ważne poświadczenie zabezpieczeń.
   
    ![Microsoft konta — wygenerować nowe hasło](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Konto Microsoft — nowe hasło](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Sprawdź pole **Obsługa zestaw Live SDK** w obszarze **zaawansowane opcje** sekcji. Kliknij pozycję **Zapisz**.
   
    ![Konto Microsoft — Obsługa zestaw Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Skonfiguruj konto Microsoft jako dostawca tożsamości w Twojej dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "MSA".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **konta Microsoft**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfigurować ten dostawca tożsamości** , a następnie wprowadź identyfikator aplikacji i hasło utworzone wcześniej aplikacji konta Microsoft.
7. Kliknij przycisk **OK** , a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta Microsoft.

