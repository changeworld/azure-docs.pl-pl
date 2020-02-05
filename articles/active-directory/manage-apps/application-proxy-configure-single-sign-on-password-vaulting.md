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
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025745"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji

Usługa Azure Active Directory serwera Proxy aplikacji pomaga zwiększyć wydajność przez pracowników zdalnych można bezpiecznie uzyskiwać do nich dostęp, zbyt publikowania lokalnych aplikacji. W witrynie Azure portal możesz również skonfigurować logowanie jednokrotne (SSO) do tych aplikacji. Użytkownicy potrzebują tylko do uwierzytelniania za pomocą usługi Azure AD, a ich dostęp do Twoich aplikacji enterprise bez konieczności ponownego zalogowania.

Serwer Proxy aplikacji obsługuje kilka [pojedynczego logowania jednokrotnego tryby](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Logowanie na podstawie hasła jest przeznaczony dla aplikacji, które używają kombinacji nazwy użytkownika i hasła do uwierzytelniania. Podczas konfigurowania opartego na hasłach logowania jednokrotnego dla aplikacji, użytkownicy muszą zalogować się do aplikacji lokalnej jeden raz. Po tym usługi Azure Active Directory są przechowywane informacje logowania i automatycznie przekazuje go do aplikacji w przypadku użytkowników dostępu zdalnego.

Powinna już opublikowania i przetestować aplikację za pomocą serwera Proxy aplikacji. Jeśli nie, wykonaj kroki opisane w [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md) następnie wróć tutaj.

## <a name="set-up-password-vaulting-for-your-application"></a>Skonfiguruj hasło vaulting dla aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
1. Wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
1. Z listy wybierz aplikację, która ma zostać skonfigurowane przy użyciu logowania jednokrotnego.  
1. Wybierz **serwera Proxy aplikacji**. 
1. Zmień **Typ uwierzytelniania wstępnego** na **Passthrough** i wybierz pozycję **Zapisz**. Później możesz ponownie wrócić do typu **Azure Active Directory** . 
1. Wybierz **logowanie jednokrotne**.

   ![Wybierz pozycję Logowanie jednokrotne na stronie Przegląd aplikacji](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Dla trybu logowania jednokrotnego wybierz **opartego na hasłach logowania jednokrotnego**.
1. Adres URL logowania wprowadź adres URL strony, gdzie użytkownicy wprowadzają swoją nazwę i hasło do logowania do aplikacji spoza sieci firmowej. Może to być zewnętrzny adres URL, który został utworzony podczas publikowania aplikacji za pośrednictwem serwera Proxy aplikacji.

   ![Wybierz opartego na hasłach logowania jednokrotnego, a następnie wprowadź adres URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Wybierz pozycję **Zapisz**.
1. Wybierz **serwera Proxy aplikacji**. 
1. Zmień **typ wstępnego uwierzytelniania** na **Azure Active Directory** i wybierz pozycję **Zapisz**. 
1. Wybierz pozycję **Użytkownicy i grupy**.
1. Przypisz użytkowników do aplikacji, wybierając pozycję **Dodaj użytkownika**. 
1. Jeśli chcesz wstępnie zdefiniować poświadczenia dla użytkownika, zaznacz pole wyboru Nazwa użytkownika i wybierz pozycję **Aktualizuj poświadczenia**.
1. Wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **wszystkie aplikacje**.
1. Z listy wybierz aplikację, którą skonfigurowano z logowaniem jednokrotnym przy użyciu hasła.
1. Wybierz **znakowanie**. 
1. Zaktualizuj **adres URL strony głównej** przy użyciu **adresu URL logowania na** stronie Logowanie jednokrotne, a następnie wybierz pozycję **Zapisz**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testowanie aplikacji

Przejdź do portalu My Apps. Zaloguj się przy użyciu swoich poświadczeń (lub poświadczeń dla konta testowego, które zostało skonfigurowane przy użyciu programu Access). Po pomyślnym zalogowaniu kliknij ikonę aplikacji. Może to spowodować zainstalowanie rozszerzenia przeglądarki Moje aplikacje bezpieczne logowanie. Jeśli użytkownik ma wstępnie zdefiniowane poświadczenia, uwierzytelnianie w aplikacji powinno nastąpić automatycznie. w przeciwnym razie należy określić nazwę użytkownika lub hasło po raz pierwszy. 

## <a name="next-steps"></a>Następne kroki

- Przeczytaj na temat innych sposobów implementowania [logowanie jednokrotne](what-is-single-sign-on.md)
- Dowiedz się więcej o [zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji, które zdalnie za pomocą serwera Proxy aplikacji usługi Azure AD](application-proxy-security.md)
