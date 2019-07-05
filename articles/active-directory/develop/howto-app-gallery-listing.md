---
title: Umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę aplikacji, która obsługuje logowanie jednokrotne w galerii aplikacji usługi Azure Active Directory
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
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41b407b4343db4f594049c4b1027fe4279dab840
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482953"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Instrukcje: Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory

W tym artykule przedstawiono sposób listy aplikacji w galerii aplikacji usługi Azure AD, implementowanie logowania jednokrotnego (SSO) i zarządzanie listy.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galerii aplikacji usługi Azure AD?

- Klienci znaleźć najlepsze możliwe logowania jednokrotnego.
- Konfiguracja aplikacji jest proste i minimalne.
- Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.
- Bezpłatna, podstawowa, i wszystkich klientów usługi Azure AD Premium mogą używać tej integracji.
- Klienci wzajemnego uzyskać samouczek krok po kroku konfiguracji.
- Klienci, którzy używają Standard SCIM można użyć, inicjowanie obsługi administracyjnej dla tej samej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacje Sfederowane (Open ID i SAML/WS-Fed) aplikacja musi obsługiwać ten model do umieszczenia w galerii usługi Azure AD. Aplikacje w przedsiębiorstwie galerii powinien obsługiwać wielu konfiguracji klienta i nie są ustawiane określonego klienta.

- Dla Open ID Connect, aplikacja powinna być wielodostępnych i [platformy wyrażania zgody w usłudze Azure AD](consent-framework.md) powinna być poprawnie zaimplementowany dla aplikacji. Użytkownika można wysyłać żądania logowania do endpoint wspólnego, tak, aby każdy klient może zapewnić zgody na aplikację. Możesz kontrolować dostęp użytkowników na podstawie Identyfikatora dzierżawy i nazwa UPN użytkownika odebrane w tokenie.

- Dla protokołu SAML 2.0/WS-Fed aplikacja musi mieć możliwość celu integracja logowania jednokrotnego SAML/WS-Fed w trybie SP lub dostawcy tożsamości. Upewnij się, że to działa poprawnie przed przesłaniem żądania.

- Hasło logowania jednokrotnego upewnij się, obsługuje uwierzytelnianie przy użyciu formularza w aplikacji, więc haseł może odbywać się uzyskać pracy rejestracji jednokrotnej, zgodnie z oczekiwaniami.

- Automatyczne inicjowanie obsługi użytkowników żądań aplikacji powinny figurować w galerii z funkcją logowania jednokrotnego pojedynczego włączane przy użyciu protokołu SAML 2.0/WS-Fed. Możesz poprosić potrzeby logowania jednokrotnego i Inicjowanie obsługi administracyjnej ze sobą w portalu, jeśli go jeszcze nie znajduje się użytkownik.

>[!NOTE]
>Będziemy działają z dużą liczbę żądań łącznika Standard SCIM, więc została zatrzymana, biorąc nowe żądania w portalu. Wstrzymaj na żądań do odwołania. Firma Microsoft przeprosinami dla tego opóźnienia i wszelkie ewentualne utrudnienia.

## <a name="submit-the-request-in-the-portal"></a>Przesyłanie żądania w portalu

Po przetestowaniu, że usługi integracji aplikacji działa z usługą Azure AD, należy przesłać żądanie dostępu na naszych [Portal sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, używać, aby zalogować się do tego portalu. W przeciwnym razie użyj konta Microsoft (np. Outlook lub Hotmail) do logowania.

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, skontaktuj się z [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) i podaj konto e-mail, którego chcesz użyć do przesyłania żądania. Zespół usługi Azure AD zostanie następnie dodaj konto w portalu sieci aplikacji firmy Microsoft.

![Żądanie dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Po dodaniu konta, możesz zalogować się do portalu sieci aplikacji firmy Microsoft.

A jeśli po zalogowaniu zostanie wyświetlona następująca strona, podać uzasadnienie biznesowe na potrzeby uzyskiwania dostępu w polu tekstowym, a następnie wybierz **żądanie dostępu**.

  ![Żądanie dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nasz zespół przegląda szczegółowe informacje i umożliwia dostęp do odpowiednio. Po zatwierdzeniu żądania możesz zalogować się do portalu i przesłać żądanie, klikając **przesłać żądanie (ISV)** Kafelek formularz na stronie głównej.

![Portal programu SharePoint strony głównej](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementowanie logowania jednokrotnego przy użyciu protokołu federation

Aby wyświetlić listę aplikacji w galerii aplikacji Azure AD, należy najpierw wdrożyć jedną z następujących protokołów Federacji obsługiwane przez usługę Azure AD i Akceptuję warunki galerii aplikacji usługi Azure AD. Przeczytaj warunki i postanowienia galerii aplikacji usługi Azure AD z [tutaj](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Aby zintegrować aplikację z usługą Azure AD przy użyciu protokołu Open ID Connect, wykonaj [instrukcje deweloperów](authentication-scenarios.md).

    ![Oś czasu wyświetlanie OpenID Connect aplikacji w galerii](./media/howto-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać aplikację do listy w galerii przy użyciu protokołu OpenID Connect, wybierz **OpenID Connect i OAuth 2.0** tak jak powyżej.
    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2.0** lub **WS-Fed**: Jeśli aplikacja obsługuje SAML 2.0, można zintegrować go bezpośrednio z dzierżawy usługi Azure AD przy użyciu [instrukcje dotyczące dodawania aplikacji niestandardowej](../active-directory-saas-custom-apps.md).

  ![Oś czasu wyświetlanie protokołu SAML 2.0 i WS-Fed aplikacji w galerii](./media/howto-app-gallery-listing/saml.png)

  * Jeśli chcesz dodać aplikację do listy w galerii, używając **SAML 2.0** lub **WS-Fed**, wybierz opcję **SAMl 2.0/WS-Fed** tak jak powyżej.
  * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementowanie logowania jednokrotnego przy użyciu hasła logowania jednokrotnego

Utwórz aplikację internetową, która zawiera strony logowania HTML do skonfigurowania [opartego na hasłach logowania jednokrotnego](../manage-apps/what-is-single-sign-on.md). Logowanie Jednokrotne oparte na hasłach, określane również jako hasło vaulting, pozwala na zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których kilka użytkownicy potrzebują do udostępnienia jednego konta, takie jak do kont aplikacji mediów społecznościowych w Twojej organizacji.

![Oś czasu wyświetlanie hasło logowania jednokrotnego aplikacji w galerii](./media/howto-app-gallery-listing/passwordsso.png)

* Jeśli chcesz dodać aplikację do listy w galerii przy użyciu logowania jednokrotnego hasła, wybierz **hasło logowania jednokrotnego** tak jak powyżej.
* Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Aktualizowanie/Usuwanie istniejącej listy

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji Azure AD, najpierw musisz przesłać żądanie w [Portal sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, używać, aby zalogować się do tego portalu. W przeciwnym razie użyj konta Microsoft (np. Outlook lub Hotmail) do logowania.

- Wybierz odpowiednią opcję, jak pokazano na poniższej ilustracji:

    ![Oś czasu wyświetlania listy aplikacji saml w galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Jeśli chcesz zaktualizować istniejącą aplikację, wybierz opcję **aktualizacji istniejącej listy aplikacji**.
    * Aby usunąć istniejącą aplikację z galerii usługi Azure AD, należy zaznaczyć **usunięcie istniejącej listy aplikacji**.
    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Wyświetlanie listy żądań przez klientów

Klienci mogą przesyłać żądania listę aplikacji, klikając **aplikacja żąda przez klientów** -> **Prześlij nowe żądanie**.

![Pokazuje klient zażądał kafelka aplikacji](./media/howto-app-gallery-listing/customer-submit-request.png)

Poniżej znajduje się przepływ klienta wymagane aplikacje —

![Pokazuje klient zażądał przepływu aplikacji](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Osie czasu

Oś czasu dla procesu wyświetlania listy protokołu SAML 2.0 i WS-Fed aplikacji w galerii jest 7 – 10 dni roboczych.

   ![Oś czasu wyświetlania listy aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline.png)

Oś czasu dla procesu o ofercie w galerii aplikacji OpenID Connect jest 2 – 5 dni roboczych.

   ![Oś czasu wyświetlania listy aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskalacji

Odpowiedzi na wszystkie pytania techniczne, Wyślij e-mail na adres [zespołu integracja logowania jednokrotnego usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) czyli SaaSApplicationIntegrations@service.microsoft.com i firma Microsoft odpowie najszybciej, jak to możliwe.
