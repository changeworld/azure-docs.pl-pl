---
title: Przekazywanie tokenu dostępu za pomocą przepływu użytkownika do aplikacji — Azure Active Directory B2C
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2,0 jako rolę w przepływie użytkownika w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a1a9ad0c615e50db746548a1ed5b557fe6fdea5a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064296"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pomocą przepływu użytkownika do aplikacji w Azure Active Directory B2C

[Przepływ użytkownika](active-directory-b2c-reference-policies.md) w Azure Active Directory B2C (Azure AD B2C) zapewnia użytkownikom aplikacji możliwość rejestrowania się lub logowania za pomocą dostawcy tożsamości. Po rozpoczęciu podróży Azure AD B2C otrzymuje [token dostępu](active-directory-b2c-reference-tokens.md) od dostawcy tożsamości. Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. W przepływie użytkownika można włączyć funkcję przekazywania tokenu do aplikacji, które są rejestrowane w Azure AD B2C.

Azure AD B2C obecnie obsługuje tylko przekazywanie tokenu dostępu dla dostawców tożsamości [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) , w tym [Facebook](active-directory-b2c-setup-fb-app.md) i [Google](active-directory-b2c-setup-goog-app.md). W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja musi używać [przepływu użytkownika w wersji 2](user-flow-versions.md).
* Przepływ użytkownika jest konfigurowany za pomocą dostawcy tożsamości OAuth 2,0.

## <a name="enable-the-claim"></a>Włączanie tego żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)** , a następnie wybierz swój przepływ użytkownika. Na przykład **B2C_1_signupsignin1**.
5. Wybierz pozycję **Oświadczenia aplikacji**.
6. Włącz funkcję **token dostępu dostawcy tożsamości** .

    ![Włączanie żądania tokenu dostępu dostawcy tożsamości](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kliknij przycisk **Zapisz** , aby zapisać przepływ użytkownika.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

W przypadku testowania aplikacji w Azure AD B2C może być przydatne, aby token `https://jwt.ms` Azure AD B2C mógł sprawdzić w nim oświadczenia.

1. Na stronie Przegląd przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
2. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token w poniższym przykładzie, **adres URL odpowiedzi** powinien być widoczny `https://jwt.ms`.
3. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu poświadczeń konta. W ramach żądania **idp_access_token** powinien zostać wyświetlony token dostępu dostawcy tożsamości.

    Powinieneś zobaczyć coś podobnego do poniższego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Następne kroki

Więcej informacji znajduje się w temacie [Omówienie tokenów Azure AD B2C](active-directory-b2c-reference-tokens.md).
