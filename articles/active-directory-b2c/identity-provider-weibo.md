---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta Weibo
titleSuffix: Azure AD B2C
description: Zapewnij klientom rejestrację i logowanie za pomocą kont Weibo w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187903"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta Weibo przy użyciu usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Tworzenie aplikacji Weibo

Aby użyć konta Weibo jako dostawcy tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta Weibo, możesz zarejestrować się w [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Zaloguj się do [portalu deweloperskiego Weibo](https://open.weibo.com/) przy użyciu poświadczeń konta Weibo.
1. Po zalogowaniu się wybierz nazwę wyświetlaną w prawym górnym rogu.
1. Z listy rozwijanej wybierz pozycję 中田田**田田田**田田 (edytuj informacje o deweloperze).
1. Wprowadź wymagane informacje i wybierz opcję 中**田**(submit).
1. Ukończ proces weryfikacji wiadomości e-mail.
1. Przejdź do [strony weryfikacji tożsamości](https://open.weibo.com/developers/identity/edit).
1. Wprowadź wymagane informacje i wybierz opcję 中**田**(submit).

### <a name="register-a-weibo-application"></a>Zarejestruj aplikację Weibo

1. Przejdź do [nowej strony rejestracji aplikacji Weibo](https://open.weibo.com/apps/new).
1. Wprowadź niezbędne informacje o aplikacji.
1. Wybierz **创建** opcję 中田 (tworzenie).
1. Skopiuj wartości **klucza aplikacji** i **klucza tajnego aplikacji**. Oba te elementy muszą dodać dostawcę tożsamości do dzierżawy.
1. Prześlij wymagane zdjęcia i wprowadź niezbędne informacje.
1. Wybierz opcję 中田田田**田**田 (zapisz).
1. Wybierz **高级信息** opcję 中田田田 (informacje zaawansowane).
1. Wybierz **编辑** pozycję 中田 (edytuj) obok pola dla OAuth2.0 **授权设置**中田田 (adres URL przekierowania).
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` adres URL przekierowania OAuth2.0. **授权设置** Jeśli na przykład nazwa dzierżawy to contoso, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`ustaw adres URL jako .
1. Wybierz opcję 中**田**(submit).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurowanie konta Weibo jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję **Weibo (Wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład *Weibo*.
1. W przypadku identyfikatora **klienta**wprowadź klucz aplikacji Weibo, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź zarejestrowany klucz tajny aplikacji.
1. Wybierz **pozycję Zapisz**.
