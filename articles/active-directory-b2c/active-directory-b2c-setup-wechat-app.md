---
title: Konfigurowanie rejestracji i logowania za pomocą konta WeChat przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom Zarejestruj się i zaloguj się przy użyciu kont WeChat w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e88187c5035abc28ca9deecaf8517e8a21e38d1d
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952338"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta WeChat przy użyciu usługi Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-wechat-application"></a>Tworzenie aplikacji WeChat

Aby użyć konta WeChat jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta WeChat, można uzyskać informacji o [ http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Rejestrowanie aplikacji WeChat

1. Zaloguj się do [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) przy użyciu swoich poświadczeń WeChat.
2. Wybierz**管理中心**(Centrum zarządzania).
3. Postępuj zgodnie z instrukcjami Aby zarejestrować nową aplikację.
4. Wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład jeśli Twoja `tenant_name` jest contoso.onmicrosoft.com, Ustaw adres URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Kopiuj **Identyfikatora aplikacji** i **klucz aplikacji**. Należy je dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie WeChat jako dostawcy tożsamości w dzierżawie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-wechat-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-wechat-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *WeChat*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **WeChat (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz aplikacji, które są rejestrowane jako **klucz tajny klienta** programu Aplikacja WeChat, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację WeChat.

