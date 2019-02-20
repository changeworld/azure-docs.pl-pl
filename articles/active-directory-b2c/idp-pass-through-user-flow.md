---
title: Przekaż token dostępu za pośrednictwem przepływu użytkownika do aplikacji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przekazać za pośrednictwem tokenu dostępu dla dostawców tożsamości OAuth 2.0 jako oświadczenia w przepływie użytkownika w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fd0f8241c07f603089b896dcfc9ece29f1e33d1c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428208"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pośrednictwem przepływu użytkownika do aplikacji w usłudze Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.

A [przepływ użytkownika](active-directory-b2c-reference-policies.md) w usłudze Azure Active Directory (Azure AD) B2C umożliwia użytkownikom aplikacji Zarejestruj się lub zaloguj się przy użyciu dostawcy tożsamości. Po uruchomieniu podróży usługi Azure AD B2C odbiera [token dostępu](active-directory-b2c-reference-tokens.md) od dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu, można pobrać informacji o użytkowniku. Możesz włączyć oświadczenia w przepływie użytkownika do przekazania tokenu za pośrednictwem aplikacji, które należy zarejestrować w usłudze Azure AD B2C.

Usługa Azure AD B2C obsługuje przekazywanie tokenu dostępu [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) i [OpenID Connect](active-directory-b2c-reference-oidc.md) dostawców tożsamości. W przypadku innych dostawców tożsamości oświadczenie zwracany jest pusty.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja musi używać [przepływ użytkownika v2](user-flow-versions.md).
- Przepływ użytkownika jest skonfigurowany za pomocą protokołu OAuth 2.0 lub OpenID Connect dostawcy tożsamości.

## <a name="enable-the-claim"></a>Włączanie oświadczenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika**, a następnie wybierz pozycję przepływ użytkownika. Na przykład **B2C_1_SignupSignIn**.
5. Wybierz pozycję **Oświadczenia aplikacji**.
6. Włącz **Token dostępu do dostawcy tożsamości**.

    ![Oświadczenia aplikacji](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kliknij przycisk **Zapisz** Aby zapisać przepływ użytkownika.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

Podczas testowania aplikacji w usłudze Azure AD B2C, może być przydatne do ma tokenu usługi Azure AD B2C, powrót do `https://jwt.ms` do przeglądania oświadczeń w nim.

1. Na stronie Przegląd przepływ użytkownika, wybierz **uruchomić przepływ użytkownika**.
2. Aby uzyskać **aplikacji**, wybierz swoją aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token w poniższym przykładzie **adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie zaloguj się przy użyciu poświadczeń konta. Powinien zostać wyświetlony token dostępu do dostawcy tożsamości w **idp_access_token** oświadczenia.

    Powinien zostać wyświetlony podobny do poniższego przykładu:

    ![Token zdekodowany](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o tokenów w [odwołania do tokenu usługi Azure Active Directory](active-directory-b2c-reference-tokens.md).




