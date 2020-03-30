---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta WeChat
titleSuffix: Azure AD B2C
description: Zapewnij klientom rejestrację i logowanie za pomocą kont WeChat w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184439"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta WeChat przy użyciu usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Tworzenie aplikacji WeChat

Aby użyć konta WeChat jako dostawcy tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta WeChat, możesz uzyskać [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)informacje na temat .

### <a name="register-a-wechat-application"></a>Zarejestruj aplikację WeChat

1. Zaloguj się [https://open.weixin.qq.com/](https://open.weixin.qq.com/) za pomocą poświadczeń WeChat.
1. Wybierz opcję 中**田田田 (centrum** zarządzania).
1. Wykonaj kroki, aby zarejestrować nową aplikację.
1. Wprowadź `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adres** URL wywołania zwrotnego. Jeśli na przykład nazwa dzierżawy to contoso, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`ustaw adres URL jako .
1. Skopiuj **identyfikator aplikacji** i **klucz aplikacji**. Będą one potrzebne, aby dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie WeChat jako dostawcy tożsamości w dzierżawie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **dostawców tożsamości**, a następnie wybierz **WeChat (Wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład *WeChat*.
1. W przypadku identyfikatora **klienta**wprowadź identyfikator aplikacji WeChat, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź klucz aplikacji, który został nagrany.
1. Wybierz **pozycję Zapisz**.
