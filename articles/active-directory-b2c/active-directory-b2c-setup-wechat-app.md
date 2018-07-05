---
title: WeChat konfiguracji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników z kontami WeChat w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a18d41a4f45b147790a17664156659d282e710d4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445961"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami WeChat

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-wechat-application"></a>Tworzenie aplikacji WeChat

Aby użyć WeChat jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację WeChat i dostarczyć odpowiednie parametry. Wymagane jest konto WeChat, aby to zrobić. Jeśli nie masz, możesz uzyskać, rejestrując się za pomocą jednego z ich aplikacji mobilnych lub przy użyciu numeru q. Po tym Utwórz konto zarejestrowane w usłudze WeChat programie dla deweloperów. Więcej informacji można znaleźć [tutaj](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Rejestrowanie aplikacji WeChat

1. Przejdź do witryny [https://open.weixin.qq.com/](https://open.weixin.qq.com/) i zaloguj się.
2. Kliknij pozycję**管理中心**(Centrum zarządzania).
3. Wykonaj niezbędne kroki, aby zarejestrować nową aplikację.
4. Aby uzyskać**授权回调域**(adres URL wywołania zwrotnego), wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Na przykład jeśli Twoja `tenant_name` jest contoso.onmicrosoft.com, Ustaw adres URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Znajdź i skopiuj **Identyfikatora aplikacji** i **klucz aplikacji**. Będą potrzebne później.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie WeChat jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "WeChat".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **WeChat**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
7. Wprowadź **klucz aplikacji** skopiowany wcześniej jako **identyfikator klienta**.
8. Wprowadź **klucz tajny aplikacji** skopiowany wcześniej jako **klucz tajny klienta**.
9. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację WeChat.

