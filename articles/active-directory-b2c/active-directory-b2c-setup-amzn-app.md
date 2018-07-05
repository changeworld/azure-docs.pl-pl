---
title: Konfiguracja Amazon w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników za pomocą konta Amazon w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e3b3d66b913b595e68c03b68990d1a4806952579
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443706"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników za pomocą konta Amazon
## <a name="create-an-amazon-application"></a>Tworzenie aplikacji Amazon
Aby użyć Amazon jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację Amazon i dostarczyć odpowiednie parametry. Wymagane jest konto Amazon, aby to zrobić. Jeśli nie masz, możesz pobrać na stronie [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Przejdź do [Centrum deweloperów Amazon](https://login.amazon.com/) i zaloguj się przy użyciu poświadczeń konta Amazon.
2. Jeśli jeszcze tego nie zrobiono, kliknij przycisk **Zarejestruj**, postępuj zgodnie z instrukcjami rejestracji dla deweloperów i zaakceptować zasady.
3. Kliknij przycisk **zarejestrować nową aplikację**.
   
    ![Rejestrowanie nowej aplikacji w witrynie sieci Web Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Podaj informacje o aplikacji (**nazwa**, **opis**, i **adres URL zasad zachowania powiadomienie**) i kliknij przycisk **Zapisz**.
   
    ![Zapewnianie informacji o aplikacji do zarejestrowania nowej aplikacji na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. W **ustawień sieci Web** sekcji, skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. (Należy kliknąć przycisk **wyświetlić wpis tajny** przycisk, aby wyświetlić to.) Należy dysponować go o skonfigurowanie Amazon jako dostawcy tożsamości w dzierżawie. Kliknij przycisk **Edytuj** w dolnej części sekcji. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
   
    ![Podanie Identyfikatora klienta oraz klucz tajny klienta dla nowej aplikacji na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Wprowadź `https://login.microsoftonline.com` w **dozwolone źródła JavaScript** pola i `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **dozwolone adresy URL zwracają** pola. Zastąp **{dzierżawa}** nazwą dzierżawy (np. contoso.onmicrosoft.com). Kliknij pozycję **Zapisz**. **{Dzierżawa}** wartości jest uwzględniana wielkość liter.
   
    ![Zapewnianie źródła JavaScript i zwraca adres URL nowej aplikacji na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie Amazon jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "Amzn".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **Amazon**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta oraz klucz tajny klienta aplikacji Amazon, która została utworzona wcześniej.
7. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Amazon.

