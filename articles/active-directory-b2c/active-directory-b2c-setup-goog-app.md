---
title: Google + konfiguracji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników za pomocą konta Google + w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe5722e8d5a0a8a5bf172577777ccb899fb7b94d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443430"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników za pomocą konta Google +
## <a name="create-a-google-application"></a>Tworzenie aplikacji Google +
Aby użyć Google + jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację Google + i dostarczyć odpowiednie parametry. Musisz mieć konto Google + w tym celu. Jeśli nie masz, możesz pobrać na stronie [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Przejdź do [konsoli deweloperów Google](https://console.developers.google.com/) i zaloguj się przy użyciu poświadczeń konta Google +.
2. Kliknij przycisk **Tworzenie projektu**, wprowadź **Nazwa projektu**, a następnie kliknij przycisk **Utwórz**.
   
    ![Google + — wprowadzenie](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + — nowy projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Kliknij przycisk **Menedżer interfejsu API** a następnie kliknij przycisk **poświadczenia** w nawigacji po lewej stronie.
4. Kliknij przycisk **ekran zgody OAuth** kartę u góry.
   
    ![Google + - poświadczeń](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Wybierz lub Określ prawidłową **adres E-mail**, podaj **nazwa produktu**i kliknij przycisk **Zapisz**.
   
    ![Google + - ekran zgody OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Kliknij przycisk **nowe poświadczenia** , a następnie wybierz **identyfikator klienta OAuth**.
   
    ![Google + - ekran zgody OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. W obszarze **typ aplikacji**, wybierz opcję **aplikacji sieci Web**.
   
    ![Google + - ekran zgody OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Podaj **nazwa** dla aplikacji, wprowadź `https://login.microsoftonline.com` w **JavaScript autoryzowanych źródeł** pola i `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **identyfikatory URI przekierowania autoryzowanych** pole. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com). **{Dzierżawa}** wartości jest uwzględniana wielkość liter. Kliknij przycisk **Utwórz**.
   
    ![Google + - Utwórz identyfikator klienta](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Konieczne będzie, obie z nich, aby skonfigurować Google + jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
   
    ![Google + - klucz tajny klienta](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie Google + jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "G +".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **Google**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta oraz klucz tajny klienta aplikacji Google +, która została utworzona wcześniej.
7. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji Google +.

