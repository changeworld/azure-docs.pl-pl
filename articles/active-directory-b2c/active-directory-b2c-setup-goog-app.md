---
title: Konfigurowanie rejestracji i logowania za pomocą konta Google - Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą kont Google w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7e092e27f73901810a9f7edd210e3513c54095e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508517"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Google przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-google-application"></a>Tworzenie aplikacji Google

Aby użyć konta Google jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Google możesz pobrać na stronie [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Zaloguj się do [konsoli deweloperów Google](https://console.developers.google.com/) przy użyciu poświadczeń konta Google.
2. W lewym górnym rogu strony, wybierz listę projektów, a następnie wybierz **nowy projekt**.
3. Wprowadź **Nazwa projektu**, kliknij przycisk **Utwórz**, a następnie upewnij się, czy przy użyciu nowego projektu.
4. Wybierz **poświadczenia** w menu po lewej stronie, a następnie wybierz **Utwórz poświadczenia** > **identyfikator klienta Oauth**.
5. W obszarze **typ aplikacji**, wybierz opcję **aplikacji sieci Web**.
6. Wprowadź **nazwa** dla aplikacji, wprowadź `https://your-tenant-name.b2clogin.com` w **JavaScript autoryzowanych źródeł**, i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **identyfikatory URI przekierowania autoryzowanych**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C.
7. Kliknij pozycję **Utwórz**.
8. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Konieczne będzie, obie z nich, aby skonfigurować Google jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Skonfiguruj konto Google jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź **nazwa**. Na przykład, wprowadź *Google*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Google**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator klienta, który wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny klienta, które są rejestrowane jako **klucz tajny klienta**aplikacji Google, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Google.

