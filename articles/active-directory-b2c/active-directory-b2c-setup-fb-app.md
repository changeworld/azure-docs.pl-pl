---
title: Konfiguracja usługi Facebook w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Umożliwiają tworzenie kont i logowania użytkowników z kontami usługi Facebook w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ceab8df79337796bed314e04bacc37bf2ebe1d00
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712321"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: Umożliwiają tworzenie kont i logowania użytkowników z kontami usługi Facebook
## <a name="create-a-facebook-application"></a>Tworzenie aplikacji usługi Facebook
Aby korzystać z usługi Facebook jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację usługi Facebook i dostarczyć parametry prawo. Należy to zrobić przy użyciu konta serwisu Facebook. Jeśli nie masz, możesz pobrać go w [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Przejdź do [Facebook dla deweloperów](https://developers.facebook.com/) poświadczenia konta witryny sieci Web i zaloguj się przy użyciu Twojej usługi Facebook.
2. Jeśli nie zostało to jeszcze zrobione, należy zarejestrować jako deweloper usługi Facebook. Aby to zrobić, kliknij przycisk **zarejestrować** (w prawym górnym rogu strony), Zaakceptuj zasady w serwisie Facebook, a następnie wykonaj kroki rejestracji.
3. Kliknij przycisk **Moje aplikacje** , a następnie kliknij przycisk **Dodaj nową aplikację**. 
4. W formularzu, należy podać **Nazwa wyświetlana** i **E-mail kontaktu**.
5. Kliknij przycisk **utworzyć identyfikator aplikacji**. Może to wymagać Zaakceptuj zasady platformy Facebook i ukończyć sprawdzania zabezpieczeń w trybie online.
6. W lewej kolumnie kliknij **ustawienia** , a następnie wybierz **podstawowe** Jeśli nie jest już wybrana.
7. Wybierz **kategorii**. 
8. Kliknij przycisk **+ Dodaj platformy** i wybierz **witryny sieci Web**.
   
    ![Facebook — ustawienia](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook — ustawienia — witryny sieci Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Wprowadź `https://login.microsoftonline.com/` w **adres URL witryny** pola, a następnie kliknij przycisk **Zapisz zmiany** w dolnej części strony.
   
    ![Facebook — adres URL witryny](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Skopiuj wartość **identyfikator aplikacji**. Kliknij przycisk **Pokaż** i skopiuj wartość **klucz tajny aplikacji**. Należy obie z nich, aby skonfigurować funkcję dostawcy tożsamości w dzierżawie usługi Facebook. **Klucz tajny aplikacji** jest ważne poświadczenie zabezpieczeń.
   
    ![Facebook - ID aplikacji i klucz tajny aplikacji](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Kliknij przycisk **+ Dodaj produktu** na lewym pasku nawigacyjnym, a następnie **Set Up** przycisk dla **logowania serwisu Facebook**.
   
    ![Facebook - logowania usługi Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Kliknij przycisk **ustawienia** na prawo nawigacji w obszarze **logowania usługi Facebook**

    ![Facebook — ustawienia logowania usługi Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **prawidłowy OAuth przekierowania URI** w **ustawień klienta OAuth** sekcji. Zastąp **{dzierżawa}** nazwą Twojej dzierżawy (na przykład contosob2c.onmicrosoft.com). Kliknij przycisk **Zapisz zmiany** w dolnej części strony.
    
    ![Facebook — identyfikator URI przekierowania uwierzytelniania OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Aby aplikacji usługi Facebook mogą być używane przez usługi Azure AD B2C, należy być publicznie udostępnione. Można to zrobić, klikając **aplikacji przejrzyj** w lewym obszarze nawigacji i włączając przełącznik w górnej części strony, aby **tak** i klikając **Potwierdź**.
    
    ![Facebook - publicznego aplikacji](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Skonfiguruj funkcję dostawcy tożsamości w dzierżawie usługi Facebook
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "Facebook".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **Facebook**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfigurować ten dostawca tożsamości** , a następnie wprowadź identyfikator aplikacji i klucz tajny aplikacji (dla aplikacji usługi Facebook, który został utworzony wcześniej) w **identyfikator klienta** i **klucz tajny klienta** odpowiednio pól.
7. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację usługi Facebook.

> [!NOTE]
> Dodawanie **dostawcy tożsamości** dzierżawcy nie modyfikuje istniejących zasad. Pamiętaj, aby zaktualizować zasad przez dołączenie utworzonego dostawcy tożsamości.
>