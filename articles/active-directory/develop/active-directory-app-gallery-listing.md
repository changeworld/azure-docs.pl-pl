---
title: Umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę aplikacji, która obsługuje logowanie jednokrotne w galerii aplikacji usługi Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/14/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: c84be8985b50be4a9339af6aa9e308341662b291
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504570"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galerii aplikacji usługi Azure AD?

Azure Active Directory (Azure AD) to oparta na chmurze Usługa zarządzania tożsamościami. [Galerii aplikacji usługi Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) znajduje się w portalu Azure Marketplace sklepu z aplikacjami, gdzie wszystkie łączniki aplikacji są publikowane do logowania jednokrotnego i aprowizacji użytkowników. Klienci, którzy używają usługi Azure AD jako dostawcy tożsamości znaleźć innego łączników aplikacji SaaS, opublikowane w tym miejscu. Administratorzy IT Dodaj łączniki z galerii aplikacji i następnie konfigurowanie i używanie łączników do logowania jednokrotnego i aprowizacji. Usługi Azure AD obsługuje wszystkie protokoły głównych federacyjnego logowania jednokrotnego, łącznie z języka SAML 2.0, OpenID Connect, OAuth i WS-Fed.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Jakie są korzyści z listą aplikacji w galerii?

*  Klienci znaleźć najlepsze możliwe logowania jednokrotnego.

*  Konfiguracja aplikacji jest proste i minimalne.

*  Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.

*  Bezpłatna, podstawowa, i wszystkich klientów usługi Azure AD Premium mogą używać tej integracji.

*  Klienci wzajemnego uzyskać samouczek krok po kroku konfiguracji.

*  Klienci, którzy używają Standard SCIM można użyć, inicjowanie obsługi administracyjnej dla tej samej aplikacji.

##  <a name="prerequisites-implement-federation-protocol"></a>Wymagania wstępne: Implementowanie federacyjnych protokołu

Aby wyświetlić listę aplikacji w galerii aplikacji Azure AD, należy najpierw wdrożyć jedną z następujących protokołów Federacji obsługiwane przez usługę Azure AD i Akceptuję warunki galerii aplikacji usługi Azure AD. Przeczytaj warunki i postanowienia galerii aplikacji usługi Azure AD z [tutaj](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/).

*   **OpenID Connect**: tworzenie wielodostępnych aplikacji w usłudze Azure AD i zaimplementować [platformy wyrażania zgody w usłudze Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework) dla aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego, aby każdy klient może zapewnić zgody na aplikację. Możesz kontrolować dostęp użytkowników na podstawie Identyfikatora dzierżawy i nazwa UPN użytkownika odebrane w tokenie. Aby zintegrować aplikację z usługą Azure AD, wykonaj [instrukcje deweloperów](authentication-scenarios.md).

    ![Oś czasu wyświetlanie OpenID Connect aplikacji w galerii](./media/active-directory-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać aplikację do listy w galerii przy użyciu protokołu OpenID Connect, wybierz **OpenID Connect i OAuth 2.0** tak jak powyżej.

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** lub **WS-Fed**: aplikacja musi mieć możliwość czy integracja logowania jednokrotnego SAML/WS-Fed w trybie SP lub dostawcy tożsamości. Jeśli aplikacja obsługuje SAML 2.0, można zintegrować go bezpośrednio z dzierżawy usługi Azure AD przy użyciu [instrukcje dotyczące dodawania aplikacji niestandardowej](../active-directory-saas-custom-apps.md).

    ![Oś czasu wyświetlanie protokołu SAML 2.0 i WS-Fed aplikacji w galerii](./media/active-directory-app-gallery-listing/saml.png)

    * Jeśli chcesz dodać aplikację do listy w galerii, używając **SAML 2.0** lub **WS-Fed**, wybierz opcję **SAMl 2.0/WS-Fed** tak jak powyżej.

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **Hasło logowania jednokrotnego**: Utwórz aplikację internetową, która zawiera strony logowania HTML do skonfigurowania [opartego na hasłach logowania jednokrotnego](../manage-apps/what-is-single-sign-on.md). Logowanie Jednokrotne oparte na hasłach, określane również jako hasło vaulting, pozwala na zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których kilka użytkownicy potrzebują do udostępnienia jednego konta, takie jak do kont aplikacji mediów społecznościowych w Twojej organizacji.

    ![Oś czasu wyświetlanie hasło logowania jednokrotnego aplikacji w galerii](./media/active-directory-app-gallery-listing/passwordsso.png)

    * Jeśli chcesz dodać aplikację do listy w galerii przy użyciu logowania jednokrotnego hasła, wybierz **hasło logowania jednokrotnego** tak jak powyżej.

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

##  <a name="updateremove-existing-listing"></a>Aktualizowanie/Usuwanie istniejącej listy

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji Azure AD, najpierw musisz przesłać żądanie w [Portal sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, używać, aby zalogować się do tego portalu. W przeciwnym razie użyj konta Microsoft (np. Outlook lub Hotmail) do logowania.

* Wybierz odpowiednią opcję z poniżej obrazu

    ![Oś czasu wyświetlania listy aplikacji saml w galerii](./media/active-directory-app-gallery-listing/updateorremove.png)
    
    * Jeśli chcesz zaktualizować istniejącą aplikację, wybierz opcję **aktualizacji istniejącej listy aplikacji**.

    * Aby usunąć istniejącą aplikację z galerii usługi Azure AD, należy zaznaczyć **usunięcie istniejącej listy aplikacji**

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Przesyłanie żądania w portalu

Po przetestowaniu, że usługi integracji aplikacji działa z usługą Azure AD, należy przesłać żądanie dostępu na naszych [Portal sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, używać, aby zalogować się do tego portalu. W przeciwnym razie użyj konta Microsoft (np. Outlook lub Hotmail) do logowania.

Po zalogowaniu się w zostanie wyświetlona następująca strona. Podać uzasadnienie biznesowe na potrzeby uzyskiwania dostępu w polu tekstowym, a następnie wybierz **żądanie dostępu**. Nasz zespół przegląda szczegółowe informacje i umożliwia dostęp do odpowiednio. Po tym można zalogować się do portalu i przesłać żądanie szczegółowe dla aplikacji.

Jeśli masz problemy dotyczące dostępu, skontaktuj się z pomocą [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Żądanie dostępu w portalu programu SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Osie czasu
    
Oś czasu dla procesu wyświetlania listy protokołu SAML 2.0 i WS-Fed aplikacji w galerii jest 7 – 10 dni roboczych.

   ![Oś czasu wyświetlania listy aplikacji saml w galerii](./media/active-directory-app-gallery-listing/timeline.png)

Oś czasu dla procesu o ofercie w galerii aplikacji OpenID Connect jest 2 – 5 dni roboczych.

   ![Oś czasu wyświetlania listy aplikacji saml w galerii](./media/active-directory-app-gallery-listing/timeline2.png)

Oś czasu dla procesu wyświetlanie aplikacji w galerii z aprowizacja obsługi użytkowników jest 40-45 dni roboczych.

   ![Oś czasu wyświetlania listy aplikacji saml w galerii](./media/active-directory-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Eskalacji

Odpowiedzi na wszystkie pytania techniczne, Wyślij e-mail na adres [zespołu integracja logowania jednokrotnego usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) czyli SaaSApplicationIntegrations@service.microsoft.com i firma Microsoft odpowie najszybciej, jak to możliwe.