---
title: Konfigurowanie rejestracji i logowania za pomocą konta Weibo przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom Zarejestruj się i zaloguj się przy użyciu kont Weibo w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 808d4bc8521917b89a7265be6dfab60757baf910
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508046"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Weibo przy użyciu usługi Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-weibo-application"></a>Tworzenie aplikacji Weibo

Aby użyć konta Weibo jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Weibo, możesz pobrać na stronie [ https://weibo.com/signup/signup.php?lang=en-us ](https://weibo.com/signup/signup.php?lang=en-us).

1. Zaloguj się do [portalu dla deweloperów Weibo](https://open.weibo.com/) przy użyciu poświadczeń konta Weibo.
2. Po zarejestrowaniu się wybierz swoją nazwę wyświetlaną w prawym górnym rogu.
3. Z listy rozwijanej wybierz**编辑开发者信息**(Edytuj informacje dla deweloperów).
4. Wprowadź wymagane informacje, a następnie wybierz pozycję**提交**(Prześlij).
5. Ukończenie procesu weryfikacji adresu e-mail.
6. Przejdź do [stronie weryfikacji tożsamości](https://open.weibo.com/developers/identity/edit).
7. Wprowadź wymagane informacje, a następnie wybierz pozycję**提交**(Prześlij).

### <a name="register-a-weibo-application"></a>Rejestrowanie aplikacji Weibo

1. Przejdź do [Nowa strona rejestracji aplikacji Weibo](https://open.weibo.com/apps/new).
2. Wprowadź informacje o wymaganych aplikacji.
3. Wybierz**创建**(Utwórz).
4. Skopiuj wartości z **klucz aplikacji** i **klucz tajny aplikacji**. Należy dysponować wyzwalaczy, aby dodać dostawcę tożsamości do dzierżawy.
5. Prześlij zdjęcia wymagane, a następnie wprowadź niezbędne informacje.
6. Wybierz**保存以上信息**(Zapisz).
7. Wybierz**高级信息**(zaawansowane informacje).
8. Wybierz**编辑**(Edytuj) obok pola dla OAuth2.0**授权设置**(adres URL przekierowania).
9. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dla OAuth2.0**授权设置**(adres URL przekierowania). Na przykład, jeśli nazwa dzierżawy firmy contoso, Ustaw adres URL, aby być `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
10. Wybierz**提交**(Prześlij).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurowanie konta Weibo jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *Weibo*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Weibo (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź klucz aplikacji, które wcześniej zarejestrowane jako **identyfikator klienta** i wprowadź klucz tajny aplikacji, które są rejestrowane jako **klucz tajny klienta** programu Aplikacja Weibo, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację Weibo.
