---
title: Wyświetlanie aplikacji w galerii aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.date: 05/09/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 5f42a706bd7cb44162765bb77039cc3173d6941e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galerii aplikacji Azure AD?

Azure Active Directory (Azure AD) to usługa oparta na chmurze tożsamości. [Galerii aplikacji Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) znajduje się w magazynie Azure Marketplace aplikacji, w której wszystkie łączniki aplikacji są publikowane rejestracji jednokrotnej i Inicjowanie obsługi użytkowników. Klienci, którzy korzystają z usługi Azure AD jako dostawcy tożsamości Znajdź różnych łączniki aplikacji SaaS, opublikowane w tym miejscu. Administratorzy IT dodać łączników w galerii aplikacji Konfigurowanie i łączniki na użytek logowania jednokrotnego i inicjowania obsługi administracyjnej. Usługi Azure AD obsługuje wszystkie protokoły głównych federacyjnego logowania jednokrotnego, łącznie z SAML 2.0, OpenID Connect, OAuth i WS-Fed.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Jakie są zalety listę aplikacji w galerii?

*  Klienci znaleźć najlepszych pojedynczy znak w aplikacji.

*  Konfiguracja aplikacji jest proste i minimalnej. 

*  Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.

*  Zwolnij, Basic i wszystkie usługi Azure AD Premium klienci mogą używać tej integracji. 

*  Wzajemne klienci uzyskują samouczek krok po kroku konfiguracji. 

*  Klienci, którzy korzystają SCIM można użyć, inicjowanie obsługi administracyjnej dla tej samej aplikacji.


##  <a name="prerequisites-implement-federation-protocol"></a>Wymagania wstępne: Protokół federacyjnego zaimplementuj

Aby wyświetlić listę aplikacji w galerii aplikacji Azure AD, należy najpierw implementować jeden z następujących protokołów federacyjnego obsługiwane przez usługę Azure AD. Przeczytaj warunki i postanowienia galerii aplikacji Azure AD, w tym miejscu. 

*   **OpenID Connect**: tworzenie wielodostępnych aplikacji w usłudze Azure AD i zaimplementować [framework zgody usługi Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego tak, aby każdy klient może zapewnić zgody do aplikacji. Można kontrolować dostęp użytkowników na podstawie Identyfikatora dzierżawy i nazwy UPN użytkownika odebrane w tokenie. Integracja aplikacji z usługą Azure AD, należy wykonać [instrukcje deweloperów](active-directory-authentication-scenarios.md).

    ![Oś czasu wyświetlanie OpenID Connect aplikacji w galerii](./media/active-directory-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać do listy aplikacji w galerii przy użyciu protokołu OpenID Connect, wybierz **OpenID Connect i OAuth 2.0** jak powyżej.

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** lub **WS-Fed**: aplikacja musi mieć możliwość czy integrację SAML/WS-Fed logowania jednokrotnego w trybie SP lub dostawców tożsamości. Jeśli aplikacja obsługuje SAML 2.0, możesz też zintegrować ją bezpośrednio z dzierżawy usługi Azure AD przy użyciu [instrukcje dotyczące dodawania aplikacji niestandardowej](../active-directory-saas-custom-apps.md).

    ![Wyświetlanie listy aplikacji SAML 2.0 i WS-Fed do galerii osi czasu](./media/active-directory-app-gallery-listing/saml.png)

    * Jeśli chcesz dodać do listy aplikacji w galerii przy użyciu **SAML 2.0** lub **WS-Fed**, wybierz pozycję **SAMl 2.0/WS-Fed** jak powyżej.

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **Hasło logowania jednokrotnego**: tworzenie aplikacji sieci web strony logowania HTML do skonfigurowania [opartego na hasłach rejestracji jednokrotnej](../manage-apps/what-is-single-sign-on.md). Logowanie Jednokrotne opartego na hasłach, także określane jako hasło vaulting, umożliwia zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których wielu użytkowników należy do udostępnienia jednego konta, takich jak do organizacji mediów społecznościowych aplikacji kont.

    ![Wyświetlanie listy aplikacji hasło logowania jednokrotnego do galerii osi czasu](./media/active-directory-app-gallery-listing/passwordsso.png)

    * Jeśli chcesz dodać do listy aplikacji w galerii przy użyciu haseł logowania jednokrotnego, wybierz **hasło logowania jednokrotnego** jak powyżej.

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

##  <a name="updateremove-existing-listing"></a>Aktualizuj lub usuń istniejące listy

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji Azure AD, należy najpierw złożyć wniosek w [Portal sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, użyj logować się do tego portalu. Jeśli nie, należy używać do logowania konta Microsoft (takich jak Outlook lub usługi Hotmail).

* Wybierz odpowiednią opcję z poniżej obrazu

    ![Wyświetlanie listy aplikacji saml do galerii osi czasu](./media/active-directory-app-gallery-listing/updateorremove.png)
    
    * Jeśli chcesz zaktualizować istniejącą aplikację, wybierz **aktualizacja istniejącej listy aplikacji**.

    * Jeśli chcesz usunąć istniejącą aplikację z galerii Azure AD, wybierz **usunięcia istniejącej listy aplikacji**

    * Jeśli masz problemy dotyczące dostępu, skontaktuj się z [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Przesyłanie żądania w portalu

Po przetestowaniu czy integracją aplikacji współpracuje z usługą Azure AD, należy przesłać żądanie dostępu na naszych [Portal sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, użyj logować się do tego portalu. Jeśli nie, należy używać do logowania konta Microsoft (takich jak Outlook lub usługi Hotmail).

Po zalogowaniu, jest wyświetlana następująca strona. Podaj uzasadnienie biznesowe na potrzeby uzyskiwania dostępu w polu tekstowym, a następnie wybierz **żądania dostępu**. Nasz zespół przegląda szczegóły i uzyskiwać dostęp w związku z tym. Po wykonaniu tej możesz zalogować się do portalu i przesłać żądanie szczegółowe dla aplikacji.

Jeśli masz problemy dotyczące dostępu, skontaktuj się z [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Żądanie dostępu w portalu programu SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Osie czasu
    
Oś czasu dla procesu wyświetlanie SAML 2.0 i WS-Fed aplikacji w galerii wynosi 7-10 dni roboczych.

   ![Wyświetlanie listy aplikacji saml do galerii osi czasu](./media/active-directory-app-gallery-listing/timeline.png)

Oś czasu dla procesu wyświetlanie OpenID Connect aplikacji w galerii jest 2-5 dni roboczych.

   ![Wyświetlanie listy aplikacji saml do galerii osi czasu](./media/active-directory-app-gallery-listing/timeline2.png)

Oś czasu dla procesu wyświetlanie aplikacji w galerii z użytkownikiem inicjowania obsługi pomocy technicznej jest 40-45 dni roboczych.

   ![Wyświetlanie listy aplikacji saml do galerii osi czasu](./media/active-directory-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Eskalacji

Na wszystkie pytania techniczne, Wyślij wiadomość e-mail do [zespołu integracji usługi Azure AD SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com) czyli SaaSApplicationIntegrations@service.microsoft.com i będzie odpowiemy tak szybko, jak to możliwe.