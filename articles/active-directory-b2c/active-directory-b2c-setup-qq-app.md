---
title: Q konfiguracji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników z kontami q w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9a0f56158f08dd3b22078f111c9ec6911b726c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444433"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami q

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-qq-application"></a>Tworzenie aplikacji q

Aby użyć q jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację q i dostarczyć odpowiednie parametry. Wymagane jest konto q, aby to zrobić. Jeśli nie masz, możesz pobrać pojedynczo [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zarejestruj się w programie dla deweloperów q

1. Przejdź do [portalu dla deweloperów q](http://open.qq.com) i zaloguj się przy użyciu poświadczeń konta q.
2. Po zalogowaniu się przejdź do [ http://open.qq.com/reg ](http://open.qq.com/reg) Aby zarejestrować się jako deweloper.
3. Wybierz z menu**个人**(poszczególnych deweloperów).
4. Wprowadź wymagane informacje w formularzu, a następnie kliknij przycisk**下一步**(następny krok).
5. Ukończenie procesu weryfikacji adresu e-mail.

> [!NOTE]
> Konieczne będzie czekać przez kilka dni w celu uzyskania zatwierdzenia po zarejestrowaniu się jako deweloper. 

### <a name="register-a-qq-application"></a>Rejestrowanie aplikacji q

1. Przejdź do obszaru [https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Ustawienia — Integracje i usługi).
2. Kliknij pozycję**应用管理**(Zarządzanie aplikacjami).
3. Kliknij pozycję**创建应用**(Tworzenie aplikacji).
4. Wprowadź informacje o wymaganych aplikacji.
5. Kliknij pozycję**创建应用**(Tworzenie aplikacji).
6. Wprowadź wymagane informacje.
7. Aby uzyskać**授权回调域**(adres URL wywołania zwrotnego) wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Na przykład jeśli Twoja `tenant_name` jest contoso.onmicrosoft.com, Ustaw adres URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Kliknij pozycję**创建应用**(Tworzenie aplikacji).
9. Na stronie potwierdzenia kliknij**应用管理**(app management), aby wrócić do strony zarządzania aplikacji.
10. Kliknij pozycję**查看**(Wyświetl) obok nowo utworzoną aplikację.
11. Kliknij pozycję**修改**(Edytuj).
12. W górnej części strony, należy skopiować **Identyfikatora aplikacji** i **klucz aplikacji**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie q jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "Q".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **q**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
7. Wprowadź **klucz aplikacji** skopiowany wcześniej jako **identyfikator klienta**.
8. Wprowadź **klucz tajny aplikacji** skopiowany wcześniej jako **klucz tajny klienta**.
9. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację q.

