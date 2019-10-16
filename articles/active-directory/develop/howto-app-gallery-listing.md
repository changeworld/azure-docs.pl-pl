---
title: Utwórz listę aplikacji w galerii aplikacji Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wyświetlić listę aplikacji, która obsługuje logowanie jednokrotne w galerii aplikacji Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c77657101f5cd8a117b2163386f6d551b7985458
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374068"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory

W tym artykule pokazano, jak wyświetlić listę aplikacji w galerii aplikacji Azure Active Directory (Azure AD), zaimplementować Logowanie jednokrotne (SSO) i zarządzać listą.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest Galeria aplikacji usługi Azure AD?

- Klienci szukają najlepszego możliwego środowiska logowania jednokrotnego.
- Konfiguracja aplikacji jest prosta i minimalna.
- Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.
- Wszyscy klienci usługi Azure AD w wersji bezpłatna, podstawowa i Premium mogą korzystać z tej integracji.
- Podzielni klienci mogą uzyskać samouczek konfigurowania krok po kroku.
- Klienci korzystający z systemu do zarządzania tożsamościami w różnych domenach ([Standard scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) mogą korzystać z aprowizacji dla tej samej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- W przypadku aplikacji federacyjnych (otwartych IDENTYFIKATORami i protokołu SAML/WS-The-karmione) aplikacja musi obsługiwać model oprogramowanie jako usługa (SaaS) w celu uzyskania listy w galerii aplikacji usługi Azure AD. Aplikacje w galerii Enterprise Gallery muszą obsługiwać wiele konfiguracji klientów, a nie klientów.
- W przypadku połączenia z programem Open ID aplikacja musi być wielodostępna, a [Struktura zgody na usługę Azure AD](consent-framework.md) musi być poprawnie zaimplementowana dla aplikacji. Użytkownik może wysłać żądanie logowania do wspólnego punktu końcowego, aby klient mógł wyrazić zgodę na dostęp do aplikacji. Możesz kontrolować dostęp użytkowników na podstawie identyfikatora dzierżawy oraz nazwy UPN użytkownika otrzymanej w tokenie.
- W przypadku protokołu SAML 2.0/WS-The---------------------------------- Przed przesłaniem żądania upewnij się, że ta funkcja działa prawidłowo.
- W przypadku logowania jednokrotnego za pomocą hasła upewnij się, że aplikacja obsługuje uwierzytelnianie formularzy, tak aby można było uruchomić Logowanie jednokrotne w oczekiwany sposób.
- Potrzebujesz stałego konta do testowania z co najmniej dwoma zarejestrowanymi użytkownikami.

## <a name="submit-the-request-in-the-portal"></a>Prześlij żądanie w portalu

Po przetestowaniu, że integracja aplikacji współpracuje z usługą Azure AD, Prześlij żądanie dostępu w [portalu sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto Office 365, użyj go do zalogowania się do tego portalu. Jeśli tak nie jest, użyj konto Microsoft, takich jak Outlook lub Hotmail, aby się zalogować.

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Podaj konto e-mail, którego chcesz użyć do przesłania żądania. Zespół usługi Azure AD doda konto w portalu sieci aplikacji firmy Microsoft.

![Komunikat żądania dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Po dodaniu konta możesz zalogować się do portalu sieci aplikacji firmy Microsoft.

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, podaj uzasadnienie biznesowe dla potrzeb dostępu w polu tekstowym. Następnie wybierz pozycję **Żądaj dostępu**.

  ![Pole uzasadnienia biznesowe w portalu programu SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nasz zespół przegląda szczegóły i zapewnia odpowiednie uzyskanie dostępu. Po zatwierdzeniu żądania możesz zalogować się do portalu i przesłać żądanie, wybierając kafelek **Prześlij żądanie (ISV)** na stronie głównej.

![Kafelek przesyłania żądania (ISV) na stronie głównej](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Zaimplementuj Logowanie jednokrotne przy użyciu protokołu federacyjnego

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, należy najpierw zaimplementować jeden z następujących protokołów federacyjnych obsługiwanych przez usługę Azure AD. Musisz również wyrazić zgodę na warunki i postanowienia galerii aplikacji usługi Azure AD. Przeczytaj warunki i postanowienia galerii aplikacji usługi Azure AD w [tej witrynie sieci Web](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect Connect**: aby zintegrować aplikację z usługą Azure AD przy użyciu protokołu połączenia Open ID Connect, postępuj zgodnie z [instrukcjami dla deweloperów](v1-authentication-scenarios.md).

    ![Wyświetlanie aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać aplikację do listy w galerii za pomocą OpenID Connect Connect, wybierz pozycję **OpenID Connect connect & OAuth 2,0** jak pokazano.
    * Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** lub **WS-karmione**: Jeśli aplikacja obsługuje protokół SAML 2,0, można zintegrować ją bezpośrednio z DZIERŻAWą usługi Azure AD, postępując zgodnie z [instrukcjami, aby dodać aplikację niestandardową](../active-directory-saas-custom-apps.md).

  ![Wyświetlanie listy aplikacji SAML 2,0 lub WS-karmione w galerii](./media/howto-app-gallery-listing/saml.png)

  * Jeśli chcesz dodać aplikację do listy w galerii za pomocą **saml 2,0** lub **WS-karmione**, wybierz pozycję **SAML 2.0/WS-karmione** , jak pokazano.
  * Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Zaimplementuj Logowanie jednokrotne przy użyciu hasła logowania jednokrotnego

Utwórz aplikację sieci Web, która zawiera stronę logowania w formacie HTML, aby skonfigurować Logowanie jednokrotne [oparte na haśle](../manage-apps/what-is-single-sign-on.md). Logowanie jednokrotne oparte na hasłach, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji.

![Wyświetlanie listy aplikacji Logowanie jednokrotne w galerii](./media/howto-app-gallery-listing/passwordsso.png)

* Jeśli chcesz dodać aplikację do listy w galerii przy użyciu hasła SSO logowania jednokrotnego, wybierz pozycję **Logowanie jednokrotne hasła** , jak pokazano.
* Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Żądanie aprowizacji użytkownika

Postępuj zgodnie z procesem przedstawionym na poniższej ilustracji, aby zażądać aprowizacji użytkowników.

   ![Żądanie aprowizacji użytkownika](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Zaktualizuj lub Usuń istniejącą listę

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji usługi Azure AD, należy najpierw przesłać żądanie w [portalu sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto Office 365, użyj go do zalogowania się do tego portalu. Jeśli tak nie jest, użyj konto Microsoft, takich jak Outlook lub Hotmail, aby się zalogować.

- Wybierz odpowiednią opcję, jak pokazano na poniższej ilustracji.

    ![Wyświetlanie listy aplikacji SAML w galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Aby zaktualizować istniejącą aplikację, wybierz odpowiednią opcję zgodnie z wymaganiami.
    * Aby usunąć istniejącą aplikację z galerii aplikacji usługi Azure AD, wybierz pozycję **Usuń moją aplikację z galerii**.
    * Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Wyświetlanie listy żądań według klientów

Klienci mogą przesłać żądanie, aby wyświetlić listę aplikacji, wybierając pozycję **żądania aplikacji przez klientów** > **Prześlij nowe żądanie**.

![Pokazuje kafelek aplikacje żądane przez klienta](./media/howto-app-gallery-listing/customer-submit-request.png)

Oto przepływ aplikacji żądanych przez klienta.

![Pokazuje przepływ aplikacji żądanych przez klienta](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Osi czasu

Oś czasu procesu tworzenia listy aplikacji SAML 2,0 lub WS-karmione w galerii wynosi od 7 do 10 dni roboczych.

  ![Oś czasu do wyświetlania aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline.png)

Oś czasu procesu tworzenia listy aplikacji OpenID Connect Connect w galerii to od 2 do 5 dni roboczych.

  ![Oś czasu do wyświetlania aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Ich eskalacji

W przypadku wszelkich eskalacji Wyślij wiadomość e-mail do [zespołu integracji rejestracji jednokrotnej usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) pod adresem SaaSApplicationIntegrations@service.microsoft.com i będziemy reagować tak szybko, jak to możliwe.
