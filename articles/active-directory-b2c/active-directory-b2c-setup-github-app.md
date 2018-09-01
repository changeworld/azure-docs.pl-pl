---
title: Konfigurowanie rejestracji i logowania za pomocą konta usługi GitHub za pomocą usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont usługi GitHub w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11f3f190c0f55e45c549a8bd1de35f78eb7b752d
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337433"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta usługi GitHub za pomocą usługi Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

Aby użyć konta usługi Github jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta usługi Github, możesz pobrać na stronie [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

1. Zaloguj się do [GitHub dla deweloperów](https://github.com/settings/developers) witryny sieci Web przy użyciu swoich poświadczeń usługi GitHub.
2. Wybierz **aplikacji OAuth** , a następnie wybierz **zarejestrowania nowej aplikacji**.
3. Wprowadź **Nazwa aplikacji** i **adres URL strony głównej**.
4. Wprowadź `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` w **adresów URL wywołania zwrotnego autoryzacji**. Zastąp **{dzierżawa}** nazwą dzierżawy usługi Azure AD B2C (na przykład contosob2c).
5. Kliknij przycisk **zarejestrować aplikację**.
6. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Należy zarówno do dodawania dostawcy tożsamości z dzierżawą.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurowanie konta usługi GitHub jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *Github*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Github (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji konto usługi Github, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta usługi Github.