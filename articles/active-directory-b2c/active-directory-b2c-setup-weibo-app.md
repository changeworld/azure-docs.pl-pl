---
title: Konfiguracja Weibo w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zapewnienie rejestracji i logowania użytkowników z kontami Weibo w swoich aplikacjach, które są zabezpieczone przez usługę Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bfd7dde290bd040f8457e6d095fdf896e802764b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444793"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Usługa Azure Active Directory B2C: Zapewnienie rejestracji i logowania użytkowników z kontami Weibo

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej. Nie należy używać tego dostawcy tożsamości w środowisku produkcyjnym.
> 

## <a name="create-a-weibo-application"></a>Tworzenie aplikacji Weibo

Aby użyć Weibo jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację Weibo i dostarczyć odpowiednie parametry. Wymagane jest konto Weibo, aby to zrobić. Jeśli nie masz, możesz pobrać pojedynczo [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Zarejestruj się w programie dla deweloperów Weibo

1. Przejdź do [portalu dla deweloperów Weibo](http://open.weibo.com/) i zaloguj się przy użyciu poświadczeń konta Weibo.
2. Po zarejestrowaniu się kliknij swoją nazwę wyświetlaną w prawym górnym rogu.
3. Z listy rozwijanej wybierz**编辑开发者信息**(Edytuj informacje dla deweloperów).
4. Wprowadź wymagane informacje w formularzu, a następnie kliknij przycisk**提交**(Prześlij).
5. Ukończenie procesu weryfikacji adresu e-mail.
6. Przejdź do [stronie weryfikacji tożsamości](http://open.weibo.com/developers/identity/edit).
7. Wprowadź wymagane informacje w formularzu, a następnie kliknij przycisk**提交**(Prześlij).

### <a name="register-a-weibo-application"></a>Rejestrowanie aplikacji Weibo

1. Przejdź do [Nowa strona rejestracji aplikacji Weibo](http://open.weibo.com/apps/new).
2. Wprowadź informacje o wymaganych aplikacji.
3. Kliknij przycisk**创建**(Utwórz).
4. Skopiuj wartości z **klucz aplikacji** i **klucz tajny aplikacji**. Będzie on potrzebny później.
5. Prześlij zdjęcia wymagane, a następnie wprowadź niezbędne informacje.
6. Kliknij przycisk**保存以上信息**(Zapisz).
7. Kliknij przycisk**高级信息**(zaawansowane informacje).
8. Kliknij przycisk**编辑**(Edytuj) obok pola dla OAuth2.0**授权设置**(adres URL przekierowania).
9. Wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` dla OAuth2.0**授权设置**(adres URL przekierowania). Na przykład jeśli Twoja `tenant_name` jest contoso.onmicrosoft.com, Ustaw adres URL jako `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Kliknij przycisk**提交**(Prześlij).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie Weibo jako dostawcy tożsamości w dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Na przykład wprowadź "Weibo".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **Weibo**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
7. Wprowadź **klucz aplikacji** skopiowany wcześniej jako **identyfikator klienta**.
8. Wprowadź **klucz tajny aplikacji** skopiowany wcześniej jako **klucz tajny klienta**.
9. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Weibo.

