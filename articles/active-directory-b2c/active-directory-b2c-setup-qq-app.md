---
title: Q konfiguracji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Umożliwiają tworzenie kont i logowania użytkowników z kontami q w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7a33a1b2a68b82b1d65b1187547695cccd7c395f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711675"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: Umożliwiają tworzenie kont i logowania użytkowników z kontami q

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-qq-application"></a>Tworzenie aplikacji q

Aby użyć q jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację q i dostarczyć prawo parametrów. Musisz mieć konto q, aby to zrobić. Jeśli nie masz, możesz ją uzyskać, w [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zarejestruj w programie dla deweloperów q

1. Przejdź do [portalu dla deweloperów q](http://open.qq.com) i zaloguj się przy użyciu poświadczeń konta q.
2. Po zalogowaniu, przejdź do [ http://open.qq.com/reg ](http://open.qq.com/reg) Aby zarejestrować się jako deweloper.
3. Wybierz z menu**个人**(poszczególnych deweloperów).
4. Wprowadź informacje wymagane do formularza, a następnie kliknij przycisk**下一步**(następny krok).
5. Ukończ proces weryfikacji wiadomości e-mail.

> [!NOTE]
> Należy poczekać kilka dni zatwierdzenia po zarejestrowaniu dewelopera. 

### <a name="register-a-qq-application"></a>Zarejestrować aplikację q

1. Przejdź do obszaru [https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Ustawienia — Integracje i usługi).
2. Polecenie**应用管理**(Zarządzanie aplikacjami).
3. Polecenie**创建应用**(Tworzenie aplikacji).
4. Wprowadź informacje niezbędne aplikacji.
5. Polecenie**创建应用**(Tworzenie aplikacji).
6. Wprowadź wymagane informacje.
7. Aby uzyskać**授权回调域**(adres URL wywołania zwrotnego) wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Na przykład jeśli Twoje `tenant_name` jest adres URL, który można ustawić contoso.onmicrosoft.com, `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Polecenie**创建应用**(Tworzenie aplikacji).
9. Na stronie potwierdzenia kliknij**应用管理**(app management), aby powrócić do strony zarządzania aplikacji.
10. Polecenie**查看**(Wyświetl) obok nowo utworzoną aplikację.
11. Polecenie**修改**(Edycja).
12. W górnej części strony, skopiuj **identyfikator aplikacji** i **klucz aplikacji**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Skonfiguruj funkcję dostawcy tożsamości w dzierżawie q
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "Q".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **q**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfigurować ten dostawca tożsamości**
7. Wprowadź **klucz aplikacji** skopiowanego wcześniej jako **identyfikator klienta**.
8. Wprowadź **klucz tajny aplikacji** skopiowanego wcześniej jako **klucz tajny klienta**.
9. Kliknij przycisk **OK** , a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację q.

