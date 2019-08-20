---
title: Konfigurowanie rejestracji i logowania przy użyciu konta usługi QQ za pomocą Azure Active Directory B2C
description: Podaj konto i zaloguj się do klientów za pomocą kont usługi QQ w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 11fc41374089c0ab7258dd191a255387713836eb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622319"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania przy użyciu konta usługi QQ za pomocą Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Tworzenie aplikacji QQ

Aby użyć konta QQ jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta QQ, możesz zarejestrować się w [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)usłudze.

### <a name="register-for-the-qq-developer-program"></a>Zarejestruj się w programie QQ Developer

1. Zaloguj się do [portalu deweloperów QQ](http://open.qq.com) przy użyciu poświadczeń konta QQ.
1. Po zalogowaniu się przejdź do [http://open.qq.com/reg](http://open.qq.com/reg) , aby zarejestrować się jako deweloper.
1. Wybierz pozycję**个人**(indywidualny deweloper).
1. Wprowadź wymagane informacje i wybierz pozycję**下一步**(Następny krok).
1. Ukończ proces weryfikacji poczty e-mail. Musisz poczekać kilka dni po zarejestrowaniu się jako deweloper.

### <a name="register-a-qq-application"></a>Rejestrowanie aplikacji QQ

1. Przejdź do obszaru [https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Ustawienia — Integracje i usługi).
1. Wybierz pozycję**应用管理**(Zarządzanie aplikacjami).
1. Wybierz pozycję**创建应用**(Utwórz aplikację) i wprowadź wymagane informacje.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład jeśli `tenant_name` jesteś contoso, ustaw adres URL `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`na.
1. Wybierz pozycję**创建应用**(Utwórz aplikację).
1. Na stronie potwierdzenie wybierz pozycję**应用管理**(Zarządzanie aplikacjami), aby powrócić do strony zarządzania aplikacjami.
1. Wybierz pozycję**查看**(widok) obok utworzonej aplikacji.
1. Wybierz pozycję**修改**(Edytuj).
1. Skopiuj **Identyfikator aplikacji** i **klucz aplikacji**. Musisz mieć obie te wartości, aby dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurowanie QQ jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **QQ (wersja zapoznawcza)** .
1. Wprowadź **nazwę**. Na przykład *QQ*.
1. W polu **Identyfikator klienta**wprowadź identyfikator aplikacji utworzonej wcześniej aplikacji QQ.
1. Dla **wpisu tajnego klienta**Wprowadź zarejestrowany klucz aplikacji.
1. Wybierz pozycję **Zapisz**.
