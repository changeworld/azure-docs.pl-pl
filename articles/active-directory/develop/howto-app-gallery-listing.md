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
ms.date: 09/06/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cf3eead3a56297efa3bea90ef48eb464ad63da0
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898516"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Instrukcje: Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory

W tym artykule pokazano, jak wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zaimplementować Logowanie jednokrotne (SSO) i zarządzać listą.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest Galeria aplikacji usługi Azure AD?

- Klienci szukają najlepszego możliwego środowiska logowania jednokrotnego.
- Konfiguracja aplikacji jest prosta i minimalna.
- Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.
- Wszyscy klienci usługi Azure AD w wersji bezpłatna, podstawowa i Premium mogą korzystać z tej integracji.
- Podzielni klienci mogą uzyskać samouczek konfigurowania krok po kroku.
- Klienci korzystający z usługi Standard scim mogą korzystać z aprowizacji dla tej samej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- W przypadku aplikacji federacyjnych (otwartych IDENTYFIKATORami i protokołu SAML/WS-karmione) aplikacja musi obsługiwać model SaaS na liście w galerii usługi Azure AD. Aplikacje w galerii Enterprise Gallery powinny obsługiwać wiele konfiguracji klientów, a nie klientów.

- W przypadku programu Open ID Connect aplikacja powinna mieć dostęp do wielu dzierżawców, a [Struktura zgody na usługę Azure AD](consent-framework.md) powinna być prawidłowo zaimplementowana dla aplikacji. Użytkownik może wysłać żądanie logowania do wspólnego punktu końcowego, aby klient mógł wyrazić zgodę na dostęp do aplikacji. Możesz kontrolować dostęp użytkowników na podstawie identyfikatora dzierżawy oraz nazwy UPN użytkownika otrzymanej w tokenie.

- W przypadku protokołu SAML 2.0/WS-karmionego aplikacja musi mieć możliwość wykonywania integracji z logowaniem jednokrotnym za pomocą protokołu SAML/WS-in w trybie SP lub dostawcy tożsamości. Przed przesłaniem żądania upewnij się, że działa poprawnie.

- W przypadku logowania jednokrotnego za pomocą hasła upewnij się, że aplikacja obsługuje uwierzytelnianie formularzy, aby można było utworzyć magazynowanie haseł w celu uzyskania obsługi logowania jednokrotnego zgodnie z oczekiwaniami.

- W przypadku automatycznych żądań aprowizacji użytkowników aplikacja powinna być wyświetlana w galerii z włączoną funkcją logowania jednokrotnego przy użyciu protokołu SAML 2.0/WS-karmionego. Możesz poprosić o Logowanie jednokrotne i obsługę administracyjną użytkowników w portalu, jeśli nie zostały one jeszcze wymienione.

- Potrzeba stałego konta do testowania z co najmniej 2 zarejestrowanymi użytkownikami.

> [!NOTE]
> Pracujemy z dużą liczbą żądań łącznika Standard scim, więc zatrzymamy nowe żądania w naszym portalu. Zaczekaj na żądania aż do dalszej uwagi. Przepraszamy za to opóźnienie i wszelkie powstałe niedogodności.

## <a name="submit-the-request-in-the-portal"></a>Prześlij żądanie w portalu

Po przetestowaniu, że integracja aplikacji współpracuje z usługą Azure AD, Prześlij żądanie dostępu do [portalu sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto Office 365, użyj go do zalogowania się do tego portalu. W przeciwnym razie zaloguj się przy użyciu konto Microsoft (na przykład Outlook lub Hotmail).

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) i podaj konto e-mail, którego chcesz użyć do przesyłania żądania. Następnie zespół usługi Azure AD doda konto w portalu sieci aplikacji firmy Microsoft.

![Żądanie dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Po dodaniu konta możesz zalogować się do portalu sieci aplikacji firmy Microsoft.

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, podaj uzasadnienie biznesowe dla potrzeb dostępu w polu tekstowym, a następnie wybierz pozycję **Żądaj dostępu**.

  ![Żądanie dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nasz zespół przegląda szczegóły i zapewnia odpowiednie uzyskanie dostępu. Po zatwierdzeniu żądania możesz zalogować się do portalu i przesłać żądanie, klikając kafelek **Prześlij żądanie (ISV)** na stronie głównej.

![Strona główna portalu programu SharePoint](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Jeśli masz jakieś problemy dotyczące dostępu, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementowanie logowania jednokrotnego przy użyciu protokołu federacyjnego

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, najpierw musisz zaimplementować jeden z następujących protokołów federacyjnych obsługiwanych przez usługę Azure AD i uzgodnić warunki i postanowienia galerii aplikacji usługi Azure AD. Przeczytaj warunki i postanowienia galerii aplikacji usługi Azure AD [tutaj](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Aby zintegrować aplikację z usługą Azure AD przy użyciu protokołu Open ID Connect, postępuj zgodnie z [instrukcjami dla deweloperów](authentication-scenarios.md).

    ![Oś czasu wyświetlania aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać aplikację do listy w galerii za pomocą OpenID Connect Connect, wybierz pozycję **OpenID Connect connect & OAuth 2,0** .
    * Jeśli masz jakieś problemy dotyczące dostępu, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2,0** lub **WS-karmione**: Jeśli aplikacja obsługuje protokół SAML 2,0, możesz zintegrować go bezpośrednio z dzierżawą usługi Azure AD przy użyciu [instrukcji, aby dodać aplikację](../active-directory-saas-custom-apps.md)niestandardową.

  ![Oś czasu na liście aplikacji SAML 2,0 lub WS-pokarmowa do galerii](./media/howto-app-gallery-listing/saml.png)

  * Jeśli chcesz dodać aplikację do listy w galerii przy użyciu protokołu **saml 2,0** lub **WS-karmione**, wybierz pozycję **SAML 2.0/WS-karmione** jako powyżej.
  * Jeśli masz jakieś problemy dotyczące dostępu, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementowanie logowania jednokrotnego przy użyciu hasła SSO

Utwórz aplikację sieci Web, która zawiera stronę logowania w formacie HTML, aby skonfigurować Logowanie jednokrotne [oparte na haśle](../manage-apps/what-is-single-sign-on.md). Logowanie jednokrotne oparte na hasłach, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji.

![Oś czasu wyświetlania aplikacji SSO z hasłem w galerii](./media/howto-app-gallery-listing/passwordsso.png)

* Jeśli chcesz dodać aplikację do listy w galerii przy użyciu hasła logowania jednokrotnego, wybierz pozycję Logowanie jednokrotne jako **hasło** powyżej.
* Jeśli masz jakieś problemy dotyczące dostępu, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Aktualizuj/Usuń istniejącą listę

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji usługi Azure AD, należy najpierw przesłać żądanie w [portalu sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto Office 365, użyj go do zalogowania się do tego portalu. W przeciwnym razie zaloguj się przy użyciu konto Microsoft (na przykład Outlook lub Hotmail).

- Wybierz odpowiednią opcję, jak pokazano na poniższej ilustracji:

    ![Oś czasu wyświetlania aplikacji SAML w galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Jeśli chcesz zaktualizować istniejącą aplikację, wybierz pozycję **Aktualizuj istniejącą aplikację**.
    * Jeśli chcesz usunąć istniejącą aplikację z galerii usługi Azure AD, wybierz pozycję **Usuń istniejącą aplikację**.
    * Jeśli masz jakieś problemy dotyczące dostępu, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Wyświetlanie żądań według klientów

Klienci mogą przesłać żądanie wystawiania aplikacji, klikając pozycję **żądania aplikacji według klientów** -> **Prześlij nowe żądanie**.

![Pokazuje kafelek aplikacje żądane przez klienta](./media/howto-app-gallery-listing/customer-submit-request.png)

Poniżej znajduje się przepływ aplikacji żądanych przez klienta —

![Pokazuje przepływ aplikacji żądanych przez klienta](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Osie czasu

Oś czasu procesu tworzenia listy aplikacji SAML 2,0 lub WS-karmione w galerii to 7-10 dni robocze.

   ![Oś czasu wyświetlania aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline.png)

Oś czasu procesu tworzenia listy aplikacji OpenID Connect Connect w galerii to 2-5 dni roboczych.

   ![Oś czasu wyświetlania aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Ich eskalacji

W przypadku wszelkich eskalacji Wyślij wiadomość e-mail do [zespołu ds. integracji rejestracji jednokrotnej usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) , który jest SaaSApplicationIntegrations@service.microsoft.com i będziemy reagować tak szybko, jak to możliwe.
