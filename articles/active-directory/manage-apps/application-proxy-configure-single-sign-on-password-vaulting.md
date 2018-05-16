---
title: Logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Włącz pojedynczego logowania do aplikacji opublikowanych lokalnymi przy użyciu aplikacji serwera Proxy Azure AD w portalu Azure.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fef163e258f797fc57d391a353b1acc148a3ef49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji

Azure Active Directory serwera Proxy aplikacji pomaga zwiększyć wydajność przez publikowanie aplikacji lokalnych, dzięki czemu pracowników zdalnych można bezpiecznie uzyskiwać do nich dostęp, zbyt. W portalu Azure można również skonfigurowaniu rejestracji jednokrotnej (SSO) do tych aplikacji. Użytkownicy potrzebują tylko do uwierzytelniania za pomocą usługi Azure AD i uzyskać dostęp do aplikacji przedsiębiorstwa bez konieczności ponownego logowania się.

Serwer Proxy aplikacji obsługuje kilka [pojedynczy logowania jednokrotnego tryby](application-proxy-single-sign-on.md). Na podstawie hasła logowania jest przeznaczony dla aplikacji, które używają kombinacja nazwy użytkownika i hasła do uwierzytelnienia. Po skonfigurowaniu opartego na hasłach logowania jednokrotnego dla aplikacji, użytkownicy muszą zalogować się do aplikacji lokalnych raz. Po wykonaniu tej usługi Azure Active Directory są przechowywane informacje logowania i automatycznie gwarantuje on aplikacji przez użytkowników dostępu zdalnego. 

Należy już zostały opublikowane i przetestować aplikację przy użyciu serwera Proxy aplikacji. Jeśli nie, postępuj zgodnie z instrukcjami [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md) następnie potem wróć tutaj. 

## <a name="set-up-password-vaulting-for-your-application"></a>Ustawianie hasła vaulting aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
3. Z listy wybierz aplikację, którą chcesz skonfigurować przy użyciu logowania jednokrotnego.  
4. Wybierz **logowanie jednokrotne**.

   ![Wybierz rejestracji jednokrotnej](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. W trybie logowania jednokrotnego, wybierz opcję **opartego na hasłach logowania jednokrotnego**.
6. Adres URL logowania wprowadź adres URL strony, gdzie użytkownicy wprowadzić swoją nazwę i hasło do logowania do aplikacji spoza sieci firmowej. Może to być zewnętrzny adres URL, który został utworzony podczas publikowania aplikacji za pośrednictwem serwera Proxy aplikacji. 

   ![Wybierz opartego na hasłach logowania jednokrotnego, a następnie wprowadź adres URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Wybierz pozycję **Zapisz**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testowanie aplikacji

Przejdź do zewnętrznego adresu URL skonfigurowanego dla dostępu zdalnego do aplikacji. Zaloguj się przy użyciu poświadczeń dla danej aplikacji (lub poświadczenia dla konta testu, które można skonfigurować przy użyciu dostępu). Po zalogowaniu pomyślnie, można pozostawić aplikacji i wrócić bez konieczności ponownego wprowadzania poświadczeń. 

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj informacje o innych sposobach zaimplementować [rejestracji jednokrotnej z serwerem Proxy aplikacji](application-proxy-single-sign-on.md)
- Dowiedz się więcej o [zagadnienia dotyczące zabezpieczeń w celu uzyskania dostępu do aplikacji zdalnie z serwera Proxy aplikacji usługi Azure AD](application-proxy-security.md)