---
title: Konfigurowanie rejestracji i logowania za pomocą konta q przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Klientom Zarejestruj się i zaloguj się przy użyciu kont q w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 38e17308f7aab5b28509d7a3509be0de844ee94f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316123"
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
7. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` w**授权回调域**(adres URL wywołania zwrotnego). Na przykład jeśli Twoja `tenant_name` jest contoso, Ustaw adres URL jako `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Wybierz**创建应用**(Tworzenie aplikacji).
9. Na stronie Potwierdzenie wybierz**应用管理**(app management), aby wrócić do strony zarządzania aplikacji.
10. Wybierz**查看**(Wyświetl) obok utworzonej aplikacji.
11. Wybierz**修改**(Edytuj).
12. Kopiuj **Identyfikatora aplikacji** i **klucz aplikacji**. Należy obu tych wartości, aby dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurowanie q jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa**. Na przykład, wprowadź *q*.
6. Wybierz **typ dostawcy tożsamości**, wybierz opcję **q (wersja zapoznawcza)** i kliknij przycisk **OK**.
7. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź zapisaną wcześniej jako identyfikator aplikacji **identyfikator klienta** i wprowadź klucz aplikacji, które są rejestrowane jako **klucz tajny klienta** z q Aplikacja, która została utworzona wcześniej.
8. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację q.

