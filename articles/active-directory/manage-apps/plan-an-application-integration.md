---
title: Rozpocząć Integrowanie usługi Azure AD z aplikacjami | Dokumentacja firmy Microsoft
description: W tym artykule jest Przewodnik z wprowadzeniem do integracji z lokalnymi aplikacjami i aplikacje w chmurze Azure Active Directory (AD).
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ac3438f0c706988323318146965a87461dcce1e8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Przewodnik Wprowadzenie do integracji Azure Active Directory z aplikacjami pobierania
## <a name="overview"></a>Przegląd
Ten temat dotyczy daje przewodnik integracji aplikacji z usługi Azure Active Directory (AD). Każdej z sekcji poniżej zawiera krótkie podsumowanie bardziej szczegółowych tematów, więc można zidentyfikować części tego przewodnika pobierania, które mają zastosowanie w przypadku.  Skorzystaj z łączy dla bardziej zgłębić temat na każdego tematu.

## <a name="before-you-begin-take-inventory"></a>Przed rozpoczęciem spis
Aby przejść w celu Integrowanie aplikacji z usługą Azure AD, należy wiedzieć, gdzie się znajdujesz, w którym ma nastąpić przejście.  Następujące kwestie mają na celu pomóc pomyśleć o projektu integracji aplikacji usługi Azure AD.

### <a name="application-inventory"></a>Spis aplikacji
* Gdzie znajdują się wszystkie aplikacje? Kto jest właścicielem je?
* Jakiego rodzaju uwierzytelniania wymagane przez aplikacje
* Który musi mieć dostęp do aplikacji, które?
* Czy chcesz wdrożyć nową aplikację?
  * Zostanie skompiluj go wewnętrznych i wdrożyć go na wystąpienie obliczeń platformy Azure?
  * Czy będziesz używać jednego, który jest dostępny w galerii aplikacji Azure?

### <a name="user-and-group-inventory"></a>Spis użytkowników i grup
* Gdy się kont użytkowników?
  * Lokalna usługa Active Directory
  * Azure AD
  * W ramach innej aplikacji bazy danych, którego jesteś właścicielem
  * W przypadku niezatwierdzonych aplikacji
  * Wszystkie powyższe
* Jakie uprawnienia i przypisania ról czy poszczególnych użytkowników aktualnie ma? Należy przejrzeć jego uprawnienia dostępu lub czy na pewno, że przypisania dostępu i role użytkowników są odpowiednie teraz?
* Grupy już istnieją w lokalnej usługi Active Directory?
  * Sposób organizowania grup
  * Które są elementami członkowskimi grupy?
  * Jakie przypisania uprawnień/ról czy aktualnie ma grupy?
* Należy wyczyścić użytkownika/grupy baz danych przed zintegrowaniem?  (Jest to bardzo ważne pytania. Odzyskiwanie w pamięci out).

### <a name="access-management-inventory"></a>Dostęp do zarządzania spisu
* Jak można obecnie zarządzać dostępem użytkowników do aplikacji? Czy, które należy zmienić?  Bierzesz pod uwagę inne sposoby zarządzania dostępu, takich jak z [RBAC](../../role-based-access-control/role-assignments-portal.md) na przykład?
* Który musi mieć dostęp do co?

Być może nie masz odpowiedzi na wszystkie pytania na początku, ale nie szkodzi.  Ten przewodnik może pomóc w odpowiedzi na niektóre z tych pytań i niektóre świadomych decyzji.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure i katalogu usługi Azure Active Directory.  Jeśli nie masz jeszcze subskrypcji platformy Azure, Azure można Wypróbuj bezpłatnie przez 30 dni. [Wypróbuj](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integracja aplikacji z usługą Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Znajdowanie niezatwierdzona aplikacji w chmurze z usługi Cloud App Discovery
Jak wspomniano powyżej, może to być aplikacje, które nie zostały zarządzanych przez organizację do tej pory.  W ramach procesu spisu prawdopodobnie można znaleźć chmury niezatwierdzonych aplikacji. Zobacz [znajdowania aplikacji w chmurze niezatwierdzone z usługi Cloud App Discovery](cloud-app-discovery.md).

### <a name="authentication-types"></a>Typy uwierzytelniania
Poszczególnych aplikacji może być wymagania dotyczące różnych uwierzytelniania. Z usługą Azure AD certyfikaty podpisywania można z aplikacji, które używają SAML 2.0, WS-Federation, lub OpenID Connect protokoły oraz jak hasło rejestracji jednokrotnej. Aby uzyskać więcej informacji o aplikacji Zobacz typy uwierzytelniania do użycia z usługą Azure AD [Zarządzanie certyfikatów dla federacyjnych rejestracji jednokrotnej w usłudze Azure Active Directory](../active-directory-sso-certs.md) i [hasła na podstawie jednokrotnego](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Włączanie rejestracji Jednokrotnej z serwera Proxy aplikacji usługi Azure AD
Dzięki serwerowi Proxy aplikacji usługi AD Microsoft Azure można zapewnić dostęp do aplikacji znajdujących się w sieci prywatnej bezpiecznego, z dowolnego miejsca i na dowolnym urządzeniu. Po zainstalowaniu łącznika serwera proxy aplikacji w danym środowisku, można można łatwo skonfigurować z usługą Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integrowanie aplikacji z usługą Azure AD
W następujących artykułach omówiono sposoby aplikacje integrują się z usługi Azure AD i niektóre wytyczne.

* [Określanie, które usługi Active Directory do użycia](../active-directory-administer.md)
* [Przy użyciu aplikacji w galerii aplikacji Azure](what-is-single-sign-on.md)
* [Integrowanie listę samouczków aplikacji SaaS](../active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
Następujące artykuły opisano sposoby możesz zarządzać dostępem do aplikacji po zostały zintegrowane z usługą Azure AD za pomocą łączników usługi Azure AD i Azure AD.

* [Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD](../active-directory-managing-access-to-apps.md)
* [Automatyzowanie z łączników usługi Azure AD](../active-directory-saas-app-provisioning.md)
* [Assigning users to an application](../active-directory-applications-guiding-developers-assigning-users.md) (Przypisywanie użytkowników do aplikacji)
* [Assigning groups to an application](../active-directory-applications-guiding-developers-assigning-groups.md) (Przypisywanie grup do aplikacji)
* [Udostępnianie kont](../active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrowanie aplikacji niestandardowych
Jeśli piszesz nowej aplikacji i chcesz, aby pomóc deweloperom w używania możliwości usługi Azure AD, zobacz [deweloperzy Guiding](../active-directory-applications-guiding-developers-for-lob-applications.md).

Jeśli chcesz dodać niestandardową aplikację do galerii aplikacji Azure, zobacz ["Przynieś własne aplikacji" w konfiguracji usługi Azure AD samoobsługi SAML](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="see-also"></a>Zobacz także
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)

