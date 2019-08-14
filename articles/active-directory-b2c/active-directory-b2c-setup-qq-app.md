---
title: Skonfiguruj konto usługi QQ i zaloguj się przy użyciu Azure Active Directory B2C | Microsoft Docs
description: Podaj konto i zaloguj się do klientów za pomocą kont usługi QQ w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9679e3216184c0dc5b8dee241c30fc69d5423aeb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963755"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania przy użyciu konta usługi QQ za pomocą Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-qq-application"></a>Tworzenie aplikacji QQ

Aby użyć konta QQ jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta QQ, możesz je uzyskać pod adresem [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zarejestruj się w programie QQ Developer

1. Zaloguj się do [portalu deweloperów QQ](http://open.qq.com) przy użyciu poświadczeń konta QQ.
2. Po zalogowaniu się przejdź do [https://open.qq.com/reg](https://open.qq.com/reg) , aby zarejestrować się jako deweloper.
3. Wybierz pozycję**个人**(indywidualny deweloper).
4. Wprowadź wymagane informacje i wybierz pozycję**下一步**(Następny krok).
5. Ukończ proces weryfikacji poczty e-mail. Musisz poczekać kilka dni po zarejestrowaniu się jako deweloper. 

### <a name="register-a-qq-application"></a>Rejestrowanie aplikacji QQ

1. Przejdź do obszaru [https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Ustawienia — Integracje i usługi).
2. Wybierz pozycję**应用管理**(Zarządzanie aplikacjami).
5. Wybierz pozycję**创建应用**(Utwórz aplikację) i wprowadź wymagane informacje.
7. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład jeśli `tenant_name` jesteś contoso, ustaw adres URL `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`na.
8. Wybierz pozycję**创建应用**(Utwórz aplikację).
9. Na stronie potwierdzenie wybierz pozycję**应用管理**(Zarządzanie aplikacjami), aby powrócić do strony zarządzania aplikacjami.
10. Wybierz pozycję**查看**(widok) obok utworzonej aplikacji.
11. Wybierz pozycję**修改**(Edytuj).
12. Skopiuj **Identyfikator aplikacji** i **klucz aplikacji**. Musisz mieć obie te wartości, aby dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurowanie QQ jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład wprowadź *QQ*.
6. Wybierz **Typ dostawcy tożsamości**, wybierz pozycję **QQ (wersja zapoznawcza)** , a następnie kliknij przycisk **OK**.
7. Wybierz opcję **Skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator aplikacji, który został zarejestrowany wcześniej jako **Identyfikator klienta** , a następnie wprowadź klucz aplikacji, który został zarejestrowany jako klucz **tajny klienta** utworzonej wcześniej aplikacji QQ.
8. Kliknij przycisk **OK** , a następnie kliknij przycisk **Utwórz** , aby zapisać konfigurację QQ.

