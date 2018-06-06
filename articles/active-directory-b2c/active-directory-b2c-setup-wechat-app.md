---
title: WeChat konfiguracji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Umożliwiają tworzenie kont i logowania użytkowników z kontami WeChat w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bbdeccbdd0d6786fdf32fc2f547344b379bd0d7c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712491"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Usługa Azure Active Directory B2C: Umożliwiają rejestracji i logowania użytkowników z kontami WeChat

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-wechat-application"></a>Tworzenie aplikacji WeChat

Aby użyć WeChat jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację WeChat i dostarczyć prawo parametrów. Należy to zrobić przy użyciu konta WeChat. Jeśli nie masz, możesz ją uzyskać, po zarejestrowaniu się za pomocą jednego z ich aplikacji mobilnych lub za pomocą numeru q. Po wykonaniu tej Pobierz konta w zarejestrowany w programie dla deweloperów WeChat. Więcej informacji można znaleźć [tutaj](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Zarejestrować aplikację WeChat

1. Przejdź do witryny [https://open.weixin.qq.com/](https://open.weixin.qq.com/) i zaloguj się.
2. Polecenie**管理中心**(management center).
3. Wykonaj kroki niezbędne, aby zarejestrować nową aplikację.
4. Aby uzyskać**授权回调域**(adres URL wywołania zwrotnego), wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Na przykład jeśli Twoje `tenant_name` jest adres URL, który można ustawić contoso.onmicrosoft.com, `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Znajdź i skopiuj **identyfikator aplikacji** i **klucz aplikacji**. Możesz będą one potrzebne później.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Skonfiguruj WeChat funkcję dostawcy tożsamości w Twojej dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "WeChat".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **WeChat**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfigurować ten dostawca tożsamości**
7. Wprowadź **klucz aplikacji** skopiowanego wcześniej jako **identyfikator klienta**.
8. Wprowadź **klucz tajny aplikacji** skopiowanego wcześniej jako **klucz tajny klienta**.
9. Kliknij przycisk **OK** , a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację WeChat.

