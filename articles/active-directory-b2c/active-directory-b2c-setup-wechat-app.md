---
title: Konfigurowanie rejestracji i logowania za pomocą konta WeChat przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom Zarejestruj się i zaloguj się przy użyciu kont WeChat w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 953868f90c4f761b1d02c314e0e1a4e04b8404d9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842615"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta WeChat przy użyciu usługi Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-wechat-application"></a>Tworzenie aplikacji WeChat

Aby użyć konta WeChat jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta WeChat, można uzyskać informacji o [ https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Rejestrowanie aplikacji WeChat

1. Zaloguj się do [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) przy użyciu swoich poświadczeń WeChat.
2. Wybierz**管理中心**(Centrum zarządzania).
3. Postępuj zgodnie z instrukcjami Aby zarejestrować nową aplikację.
4. Wprowadź `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład, jeśli nazwa dzierżawy firmy contoso, Ustaw adres URL, aby być `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Kopiuj **Identyfikatora aplikacji** i **klucz aplikacji**. Należy je dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie WeChat jako dostawcy tożsamości w dzierżawie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *WeChat*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **WeChat (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz aplikacji, które są rejestrowane jako **klucz tajny klienta** programu Aplikacja WeChat, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację WeChat.

