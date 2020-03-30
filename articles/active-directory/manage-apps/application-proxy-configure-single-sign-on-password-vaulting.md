---
title: Logowanie jednokrotne do aplikacji za pomocą serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Włącz logowanie jednokrotne dla opublikowanych aplikacji lokalnych za pomocą serwera proxy aplikacji usługi Azure AD w witrynie Azure portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025745"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Przechwalanie haseł do logowania jednokrotnego za pomocą serwera proxy aplikacji

Usługa Azure Active Directory Application Proxy pomaga zwiększyć produktywność, publikując aplikacje lokalne, dzięki czemu zdalni pracownicy mogą mieć do nich bezpieczny dostęp. W witrynie Azure portal można również skonfigurować logowanie jednokrotne (Logowanie jednokrotne) do tych aplikacji. Użytkownicy muszą tylko uwierzytelnić się za pomocą usługi Azure AD i mogą uzyskać dostęp do aplikacji dla przedsiębiorstw bez konieczności ponownego logowania.

Serwer proxy aplikacji obsługuje kilka [trybów logowania jednokrotnego.](what-is-single-sign-on.md#choosing-a-single-sign-on-method) Logowanie oparte na hasłach jest przeznaczone dla aplikacji, które używają kombinacji nazwy użytkownika i hasła do uwierzytelniania. Podczas konfigurowania logowania opartego na hasłach dla aplikacji użytkownicy muszą zalogować się do aplikacji lokalnej raz. Następnie usługa Azure Active Directory przechowuje informacje logowania i automatycznie udostępnia je aplikacji, gdy użytkownicy uzyskują do niej zdalny dostęp.

Aplikacja powinna już zostać opublikowana i przetestowana za pomocą serwera proxy aplikacji. Jeśli nie, wykonaj kroki opisane w [publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD,](application-proxy-add-on-premises-application.md) a następnie wróć tutaj.

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurowanie przechowywania haseł dla aplikacji

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
1. Wybierz**aplikacje** >  **Azure Active Directory** > Enterprise**Wszystkie aplikacje**.
1. Z listy wybierz aplikację, którą chcesz skonfigurować przy tym przyłączu SYC.  
1. Wybierz **pozycję Proxy aplikacji**. 
1. Zmień **typ uwierzytelniania wstępnego** na **Przekazywanie** i wybierz pozycję **Zapisz**. Później można ponownie przełączyć się z powrotem do typu **usługi Azure Active Directory** ponownie! 
1. Wybierz **opcję Logowanie jednokrotne**.

   ![Wybierz opcję Logowanie jednokrotne na stronie przeglądu aplikacji](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. W trybie logowania samouszeńców wybierz pozycję **Logowanie oparte na hasłach**.
1. W przypadku adresu URL logowania wprowadź adres URL strony, na której użytkownicy wejdą swoją nazwę użytkownika i hasło, aby zalogować się do aplikacji poza siecią firmową. Może to być zewnętrzny adres URL utworzony podczas publikowania aplikacji za pośrednictwem serwera proxy aplikacji.

   ![Wybierz logowanie oparte na hasłach i wprowadź swój adres URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Wybierz **pozycję Zapisz**.
1. Wybierz **pozycję Proxy aplikacji**. 
1. Zmień **typ uwierzytelniania wstępnego** na **usługę Azure Active Directory** i wybierz pozycję **Zapisz**. 
1. Wybierz **pozycję Użytkownicy i grupy**.
1. Przypisz użytkowników do aplikacji z wybraniem opcji **Dodaj użytkownika**. 
1. Jeśli chcesz wstępnie zdefiniować poświadczenia dla użytkownika, zaznacz pole przed nazwą użytkownika i wybierz pozycję **Aktualizuj poświadczenia**.
1. Wybierz**rejestracje** > aplikacji **usługi Azure Active Directory** > Wszystkie**aplikacje**.
1. Z listy wybierz aplikację skonfigurowany przy loguchałem do haseł.
1. Wybierz **opcję Znakowanie**. 
1. Zaktualizuj **adres URL strony głównej** za pomocą adresu URL logowania do znaku **URL** z strony logowania do logowania bez logowania i wybierz pozycję **Zapisz**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testowanie aplikacji

Przejdź do portalu Moje aplikacje. Zaloguj się przy użyciu poświadczeń (lub poświadczeń dla konta testowego skonfigurowanego z dostępem). Po pomyślnym zalogowaniu kliknij ikonę aplikacji. Może to spowodować instalację rozszerzenia przeglądarki My Apps Secure Sign-in. Jeśli użytkownik miał wstępnie zdefiniowane poświadczenia, uwierzytelnianie w aplikacji powinno odbywać się automatycznie, w przeciwnym razie należy określić nazwę użytkownika lub hasło po raz pierwszy. 

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o innych sposobach implementowania [logowania jednokrotnego](what-is-single-sign-on.md)
- Dowiedz się więcej [o zagadnieniach dotyczących zabezpieczeń dotyczących zdalnego uzyskiwania dostępu do aplikacji za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-security.md)
