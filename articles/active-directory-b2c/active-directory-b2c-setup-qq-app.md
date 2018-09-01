---
title: Konfigurowanie rejestracji i logowania za pomocą konta q przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom Zarejestruj się i zaloguj się przy użyciu kont q w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11bb5bf132103bed9e154a12c0e628177ca6a57a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344928"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta q przy użyciu usługi Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.
> 

## <a name="create-a-qq-application"></a>Tworzenie aplikacji q

Aby użyć konta q jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta q, możesz pobrać na stronie [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zarejestruj się w programie dla deweloperów q

1. Zaloguj się do [portalu dla deweloperów q](http://open.qq.com) przy użyciu poświadczeń konta q.
2. Po zalogowaniu się przejdź do [ http://open.qq.com/reg ](http://open.qq.com/reg) Aby zarejestrować się jako deweloper.
3. Wybierz**个人**(poszczególnych deweloperów).
4. Wprowadź wymagane informacje, a następnie wybierz pozycję**下一步**(następny krok).
5. Ukończenie procesu weryfikacji adresu e-mail. Konieczne będzie czekać przez kilka dni w celu uzyskania zatwierdzenia po zarejestrowaniu się jako deweloper. 

### <a name="register-a-qq-application"></a>Rejestrowanie aplikacji q

1. Przejdź do obszaru [https://connect.qq.com/index.html](https://connect.qq.com/index.html) (Ustawienia — Integracje i usługi).
2. Wybierz**应用管理**(Zarządzanie aplikacjami).
5. Wybierz**创建应用**(Tworzenie aplikacji) i wprowadź wymagane informacje.
7. Wprowadź `https://{tenant_name}.b2clogin.com/te/{tenant_name}.onmicrosoft.com/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład jeśli Twoja `tenant_name` jest contoso, Ustaw adres URL jako `https://contoso.b2clogin.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Wybierz**创建应用**(Tworzenie aplikacji).
9. Na stronie Potwierdzenie wybierz**应用管理**(app management), aby wrócić do strony zarządzania aplikacji.
10. Wybierz**查看**(Wyświetl) obok utworzonej aplikacji.
11. Wybierz**修改**(Edytuj).
12. Kopiuj **Identyfikatora aplikacji** i **klucz aplikacji**. Należy obu tych wartości, aby dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurowanie q jako dostawcy tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-setup-qq-app/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-setup-qq-app/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *q*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **q (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz aplikacji, które są rejestrowane jako **klucz tajny klienta** z q Aplikacja, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację q.

