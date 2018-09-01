---
title: Konfigurowanie rejestracji i logowania za pomocą konta Amazon przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Amazon w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c64b32656db2d3b821833450b4e866b9e33e44cd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337349"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Amazon przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Tworzenie aplikacji Amazon

Aby użyć konta Amazon jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Amazon możesz pobrać na stronie [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) przy użyciu poświadczeń konta Amazon.
2. Jeśli jeszcze tego nie zrobiono, kliknij przycisk **Zarejestruj**, postępuj zgodnie z instrukcjami rejestracji dla deweloperów i zaakceptować zasady.
3. Wybierz **zarejestrować nową aplikację**.
4. Wprowadź **nazwa**, **opis**, i **adres URL zasad zachowania powiadomienie**, a następnie kliknij przycisk **Zapisz**.
5. W **ustawień sieci Web** sekcji, skopiuj wartości z **identyfikator klienta**. Wybierz **wyświetlić wpis tajny** można pobrać klucza tajnego klienta, a następnie skopiuj go. Należy dysponować je, aby skonfigurować konto usługi Amazon jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. W **ustawień sieci Web** zaznacz **Edytuj**, a następnie wprowadź `https://{tenant}.b2clogin.com` w **dozwolone źródła JavaScript** i `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` w **dozwolone Zwraca adresy URL**. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c). 
7. Kliknij pozycję **Zapisz**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurowanie konta Amazon jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Amazon*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Amazon**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji Amazon, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Amazon.

