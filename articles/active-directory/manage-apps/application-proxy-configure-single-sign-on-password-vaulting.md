---
title: Logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Włącz pojedynczego logowania jednokrotnego dla aplikacji opublikowanych w środowisku lokalnym za pomocą aplikacji serwera Proxy Azure AD w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c19550adf500ba91462af12b4c5f7f5e38240e67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783513"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji

Usługa Azure Active Directory serwera Proxy aplikacji pomaga zwiększyć wydajność przez pracowników zdalnych można bezpiecznie uzyskiwać do nich dostęp, zbyt publikowania lokalnych aplikacji. W witrynie Azure portal możesz również skonfigurować logowanie jednokrotne (SSO) do tych aplikacji. Użytkownicy potrzebują tylko do uwierzytelniania za pomocą usługi Azure AD, a ich dostęp do Twoich aplikacji enterprise bez konieczności ponownego zalogowania.

Serwer Proxy aplikacji obsługuje kilka [pojedynczego logowania jednokrotnego tryby](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Logowanie na podstawie hasła jest przeznaczony dla aplikacji, które używają kombinacji nazwy użytkownika i hasła do uwierzytelniania. Podczas konfigurowania opartego na hasłach logowania jednokrotnego dla aplikacji, użytkownicy muszą zalogować się do aplikacji lokalnej jeden raz. Po tym usługi Azure Active Directory są przechowywane informacje logowania i automatycznie przekazuje go do aplikacji w przypadku użytkowników dostępu zdalnego. 

Powinna już opublikowania i przetestować aplikację za pomocą serwera Proxy aplikacji. Jeśli nie, wykonaj kroki opisane w [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md) następnie wróć tutaj. 

## <a name="set-up-password-vaulting-for-your-application"></a>Skonfiguruj hasło vaulting dla aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
3. Z listy wybierz aplikację, która ma zostać skonfigurowane przy użyciu logowania jednokrotnego.  
4. Wybierz **logowanie jednokrotne**.

   ![Wybierz opcję logowania jednokrotnego](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. Dla trybu logowania jednokrotnego wybierz **opartego na hasłach logowania jednokrotnego**.
6. Adres URL logowania wprowadź adres URL strony, gdzie użytkownicy wprowadzają swoją nazwę i hasło do logowania do aplikacji spoza sieci firmowej. Może to być zewnętrzny adres URL, który został utworzony podczas publikowania aplikacji za pośrednictwem serwera Proxy aplikacji. 

   ![Wybierz opartego na hasłach logowania jednokrotnego, a następnie wprowadź adres URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Wybierz pozycję **Zapisz**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testowanie aplikacji

Przejdź do zewnętrznego adresu URL, który został skonfigurowany dla dostępu zdalnego do aplikacji. Zaloguj się przy użyciu poświadczeń dla tej aplikacji (lub poświadczenia dla konta testowego, które można skonfigurować przy użyciu dostępu). Po zalogowaniu pomyślnie, użytkownik powinien móc opuszczaj aplikacji, a następnie powrót, bez konieczności ponownego wprowadzania poświadczeń. 

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj na temat innych sposobów implementowania [logowanie jednokrotne](what-is-single-sign-on.md)
- Dowiedz się więcej o [zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji, które zdalnie za pomocą serwera Proxy aplikacji usługi Azure AD](application-proxy-security.md)
