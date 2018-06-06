---
title: Weibo konfiguracji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Umożliwiają tworzenie kont i logowania użytkowników z kontami Weibo w aplikacjach, które są zabezpieczone przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c11931b0b1c568591a89277844722dceeac59039
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711471"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Usługa Azure Active Directory B2C: Umożliwiają rejestracji i logowania użytkowników z kontami Weibo

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej. Ten dostawca tożsamości nie należy używać w środowisku produkcyjnym.
> 

## <a name="create-a-weibo-application"></a>Tworzenie aplikacji Weibo

Aby użyć Weibo jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy utworzyć aplikację Weibo i dostarczyć prawo parametrów. Należy to zrobić przy użyciu konta Weibo. Jeśli nie masz, możesz ją uzyskać, w [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Zarejestruj w programie dla deweloperów Weibo

1. Przejdź do [portalu dla deweloperów Weibo](http://open.weibo.com/) i zaloguj się przy użyciu poświadczeń konta Weibo.
2. Po zalogowaniu kliknij swoją nazwę wyświetlaną w prawym górnym rogu.
3. Na liście rozwijanej wybierz**编辑开发者信息**(Edytuj informacje dla deweloperów).
4. Wprowadź informacje wymagane do formularza, a następnie kliknij przycisk**提交**(Prześlij).
5. Ukończ proces weryfikacji wiadomości e-mail.
6. Przejdź do [strony weryfikacji tożsamości](http://open.weibo.com/developers/identity/edit).
7. Wprowadź informacje wymagane do formularza, a następnie kliknij przycisk**提交**(Prześlij).

### <a name="register-a-weibo-application"></a>Zarejestrować aplikację Weibo

1. Przejdź do [nowej strony rejestracji aplikacji Weibo](http://open.weibo.com/apps/new).
2. Wprowadź informacje niezbędne aplikacji.
3. Kliknij przycisk**创建**(Utwórz).
4. Skopiuj wartości z **klucz aplikacji** i **klucz tajny aplikacji**. Będzie on potrzebny później.
5. Przekazywanie wymaganych zdjęć i wprowadź wymagane informacje.
6. Kliknij przycisk**保存以上信息**(Zapisz).
7. Kliknij przycisk**高级信息**(zaawansowane informacje).
8. Kliknij przycisk**编辑**(Edytuj) obok pola dla OAuth2.0**授权设置**(adres URL przekierowania).
9. Wprowadź `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` dla OAuth2.0**授权设置**(adres URL przekierowania). Na przykład jeśli Twoje `tenant_name` jest adres URL, który można ustawić contoso.onmicrosoft.com, `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Kliknij przycisk**提交**(Prześlij).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Skonfiguruj Weibo funkcję dostawcy tożsamości w Twojej dzierżawie
1. Wykonaj następujące kroki, aby [przejdź do bloku funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
2. W bloku funkcji B2C, kliknij polecenie **dostawców tożsamości**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "Weibo".
5. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **Weibo**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfigurować ten dostawca tożsamości**
7. Wprowadź **klucz aplikacji** skopiowanego wcześniej jako **identyfikator klienta**.
8. Wprowadź **klucz tajny aplikacji** skopiowanego wcześniej jako **klucz tajny klienta**.
9. Kliknij przycisk **OK** , a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Weibo.

