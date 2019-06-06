---
title: Konfigurowanie rejestrowania i zaloguj się przy użyciu konta Microsoft — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom rejestracji i logowania za pomocą konta Microsoft w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c0d236fbe350a36bc67e26349fe3c3145bd01616
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508449"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Microsoft, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft

Aby użyć konta Microsoft jako [dostawcy tożsamości](active-directory-b2c-reference-oidc.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Microsoft, możesz pobrać na stronie [ https://www.live.com/ ](https://www.live.com/).

1. Zaloguj się do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przy użyciu poświadczeń konta Microsoft.
2. W prawym górnym rogu wybierz **Dodaj aplikację**.
3. Wprowadź **nazwa** dla aplikacji. Na przykład *MSAapp1*.
4. Wybierz **wygenerować nowe hasło** i upewnij się, skopiuj hasło do użycia podczas konfigurowania dostawcy tożsamości. Także skopiować **identyfikator aplikacji**. 
5. Wybierz **platformy Dodaj**, a następnie i wybierz polecenie **Web**.
4. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **przekierowania adresów URL**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy.
5. Wybierz pozycję **Zapisz**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurowanie konta Microsoft jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *MSA*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Account Microsoft**i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator aplikacji, które wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź hasło, które są rejestrowane jako **klucz tajny klienta**aplikacji konta Microsoft, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta Microsoft.

