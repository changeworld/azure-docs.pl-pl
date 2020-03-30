---
title: Przekazywanie tokenu dostępu za pośrednictwem przepływu użytkownika do aplikacji
titleSuffix: Azure AD B2C
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2.0 jako oświadczenie w przepływie użytkownika w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187789"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Przekazywanie tokenu dostępu za pośrednictwem przepływu użytkownika do aplikacji w usłudze Azure Active Directory B2C

[Przepływ użytkownika](user-flow-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C) zapewnia użytkownikom aplikacji możliwość zarejestrowania się lub zalogowania się u dostawcy tożsamości. Po uruchomieniu usługi Azure AD B2C odbiera [token dostępu](tokens-overview.md) od dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. Włącz oświadczenie w przepływie użytkownika, aby przekazać token do aplikacji, które można zarejestrować w usłudze Azure AD B2C.

Usługa Azure AD B2C obsługuje obecnie tylko przekazywanie tokenu dostępu dostawców tożsamości [OAuth 2.0,](authorization-code-flow.md) do których należą [Facebook](identity-provider-facebook.md) i [Google](identity-provider-google.md). Dla wszystkich innych dostawców tożsamości oświadczenie jest zwracane puste.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja musi używać [przepływu użytkownika w wersji 2](user-flow-versions.md).
* Przepływ użytkownika jest skonfigurowany z dostawcą tożsamości OAuth 2.0.

## <a name="enable-the-claim"></a>Włącz reklamację

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika (zasady),** a następnie wybierz przepływ użytkownika. Na przykład **B2C_1_signupsignin1**.
5. Wybierz pozycję **Oświadczenia aplikacji**.
6. Włącz oświadczenie **tokenu dostępu dostawcy tożsamości.**

    ![Włączanie oświadczenia tokenu dostępu dostawcy tożsamości](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kliknij **przycisk Zapisz,** aby zapisać przepływ użytkownika.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

Podczas testowania aplikacji w usłudze Azure AD B2C może być przydatne, `https://jwt.ms` aby token usługi Azure AD B2C został zwrócony w celu przejrzenia oświadczeń w nim.

1. Na stronie Przegląd przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
2. W **przypadku aplikacji**wybierz aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token w poniższym przykładzie, powinien zostać wyświetlony `https://jwt.ms`adres URL **odpowiedzi** .
3. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu poświadczeń konta. Powinien zostać wyświetlony token dostępu dostawcy tożsamości w **idp_access_token** oświadczenia.

    Powinieneś zobaczyć coś podobnego do następującego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej w [ychowyzwiesz się o tokenach usługi Azure AD B2C.](tokens-overview.md)
