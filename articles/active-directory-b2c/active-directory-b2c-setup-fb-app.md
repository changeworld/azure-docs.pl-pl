---
title: Konfiguracja usługi Facebook w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników z kontami usługi Facebook w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 316e44ea92a25ab804c8cc499f91c45e4a66ef02
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445504"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami usługi Facebook
## <a name="create-a-facebook-application"></a>Tworzenie aplikacji usługi Facebook
Aby korzystać z usługi Facebook jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację usługi Facebook i dostarczyć odpowiednie parametry. Wymagane jest konto usługi Facebook, aby to zrobić. Jeśli nie masz, możesz pobrać na stronie [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Przejdź do [serwisu Facebook dla deweloperów](https://developers.facebook.com/) poświadczeń konta w witrynie sieci Web i zaloguj się przy użyciu usługi Facebook.
2. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper usługi Facebook. Aby to zrobić, kliknij przycisk **zarejestrować** (w prawym górnym rogu strony), Zaakceptuj zasady w serwisie Facebook, a następnie wykonaj kroki rejestracji.
3. Kliknij przycisk **Moje aplikacje** a następnie kliknij przycisk **Dodaj nową aplikację**. 
4. W formularzu, należy podać **nazwę wyświetlaną** i prawidłową **E-mail kontaktowy**.
5. Kliknij przycisk **utworzyć identyfikator aplikacji**. Może to wymagać zaakceptować zasady platformy usługi Facebook i ukończyć sprawdzania zabezpieczeń w trybie online.
6. W lewej kolumnie kliknij **ustawienia** , a następnie wybierz **podstawowe** Jeśli nie jest już wybrana.
7. Wybierz **kategorii**. 
8. Kliknij przycisk **+ Dodaj platformę** i wybierz **witryny sieci Web**.
   
    ![Facebook — ustawienia](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Settings - witryny sieci Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Wprowadź `https://login.microsoftonline.com/` w **adres URL witryny** pola, a następnie kliknij przycisk **Zapisz zmiany** w dolnej części strony.
   
    ![Facebook — adres URL witryny](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Skopiuj wartość **Identyfikatora aplikacji**. Kliknij przycisk **Pokaż** i skopiuj wartość **klucz tajny aplikacji**. Konieczne będzie, obie z nich, aby skonfigurować usługi Facebook jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
   
    ![Facebook — identyfikator aplikacji i klucz tajny aplikacji](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Kliknij przycisk **+ Dodaj produkt** w okienku nawigacji po lewej stronie i następnie **Set Up** przycisku **logowania do usługi Facebook**.
   
    ![Facebook - logowania do usługi Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Kliknij przycisk **ustawienia** w stronę prawego paska nawigacji w obszarze **logowania do usługi Facebook**

    ![Facebook — ustawienia logowania do usługi Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **identyfikatory URI przekierowania OAuth prawidłowe** pole **ustawienia uwierzytelniania OAuth klienta** sekcji. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com). Kliknij przycisk **Zapisz zmiany** w dolnej części strony.
    
    ![Facebook — identyfikator URI przekierowania uwierzytelniania OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Aby aplikacji usługi Facebook może być używany przez usługę Azure AD B2C, musisz udostępnić je publicznie. Można to zrobić, klikając **przeglądu aplikacji** w okienku nawigacji po lewej stronie i włączając przełącznik w górnej części strony Aby **tak** i klikając **Potwierdź**.
    
    ![Facebook - public aplikacji](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Facebook jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład, "Facebook".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **Facebook**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator aplikacji oraz wpis tajny aplikacji (z aplikacji usługi Facebook, która została utworzona wcześniej) w **identyfikator klienta** i **klucz tajny klienta** pola, odpowiednio.
7. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Facebook.

> [!NOTE]
> Dodawanie **dostawcy tożsamości** dla Twojej dzierżawy nie powoduje modyfikacji istniejących zasad. Pamiętaj, aby zaktualizować zasady przez dostawcę tożsamości, który został utworzony.
>