---
title: Skonfiguruj konto i zaloguj się przy użyciu konta Google — Azure Active Directory B2C
description: Zalogować się do klientów za pomocą kont Google w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7286bf1fd51883587aa41dc69d5dae0a3e6fb824
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622407"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi logowania i zaloguj się za pomocą konta Google przy użyciu Azure Active Directory B2C

## <a name="create-a-google-application"></a>Tworzenie aplikacji Google

Aby użyć konta Google jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta Google, możesz zarejestrować się w [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)usłudze.

1. Zaloguj się do [konsoli usługi Google Developer](https://console.developers.google.com/) przy użyciu poświadczeń konta Google.
1. W lewym górnym rogu strony wybierz listę projektu, a następnie wybierz pozycję **Nowy projekt**.
1. Wprowadź **nazwę projektu**, kliknij przycisk **Utwórz**, a następnie upewnij się, że używasz nowego projektu.
1. Z menu po lewej stronie wybierz pozycję **poświadczenia** , a następnie wybierz pozycję **Utwórz poświadczenia** > **Identyfikator klienta OAuth**.
1. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web**.
1. Wprowadź **nazwę** aplikacji `https://your-tenant-name.b2clogin.com` , wprowadź w polu **autoryzowane źródła kodu JavaScript**i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu **autoryzowane identyfikatory URI przekierowania**. Zamień `your-tenant-name` na nazwę dzierżawy. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C.
1. Kliknij przycisk **Utwórz**.
1. Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Oba te elementy będą potrzebne do skonfigurowania usługi Google jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurowanie konta Google jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Google**.
1. Wprowadź **nazwę**. Na przykład *Google*.
1. W polu **Identyfikator klienta**wprowadź identyfikator klienta utworzonej wcześniej aplikacji Google.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
