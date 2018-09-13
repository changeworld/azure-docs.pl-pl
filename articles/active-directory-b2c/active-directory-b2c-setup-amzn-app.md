---
title: Konfigurowanie rejestracji i logowania za pomocą konta Amazon przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Amazon w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5ded04f564439be332152bfad0fc571fe8eaf8c2
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720977"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Amazon przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Tworzenie aplikacji Amazon

Aby użyć konta Amazon jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Amazon możesz pobrać na stronie [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) przy użyciu poświadczeń konta Amazon.
2. Jeśli jeszcze tego nie zrobiono, kliknij przycisk **Zarejestruj**, postępuj zgodnie z instrukcjami rejestracji dla deweloperów i zaakceptować zasady.
3. Wybierz **zarejestrować nową aplikację**.
4. Wprowadź **nazwa**, **opis**, i **adres URL zasad zachowania powiadomienie**, a następnie kliknij przycisk **Zapisz**. Powiadomienie dotyczące prywatności jest strona, którą zarządzasz, która zawiera informacje o ochronie prywatności dla użytkowników.
5. W **ustawień sieci Web** sekcji, skopiuj wartości z **identyfikator klienta**. Wybierz **wyświetlić wpis tajny** można pobrać klucza tajnego klienta, a następnie skopiuj go. Należy dysponować je, aby skonfigurować konto usługi Amazon jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. W **ustawień sieci Web** zaznacz **Edytuj**, a następnie wprowadź `https://your-tenant-name.b2clogin.com` w **dozwolone źródła JavaScript** i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **dozwolone Zwraca adresy URL**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C.
7. Kliknij pozycję **Zapisz**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurowanie konta Amazon jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-amzn-app/switch-directories.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Amazon*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Amazon**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji Amazon, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Amazon.

