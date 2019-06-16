---
title: Rozpocznij pracę, Integracja usługi Azure AD z aplikacjami | Dokumentacja firmy Microsoft
description: Ten artykuł jest Przewodnik z wprowadzeniem do integrowania usługi Azure Active Directory (AD) przy użyciu aplikacji lokalnych i aplikacji w chmurze.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11453ad9132664313df2b56cb3664512628a16ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108243"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrowanie usługi Azure Active Directory z aplikacjami Wprowadzenie przewodnik wprowadzenie

Ten temat zawiera podsumowanie procesu Integrowanie aplikacji za pomocą usługi Azure Active Directory (AD). Każdy z poniższych sekcjach zawierają krótkie podsumowanie bardziej szczegółowych tematów, więc można zidentyfikować, które części w tym przewodniku z wprowadzeniem są istotne dla Ciebie.

Aby pobrać szczegóły wdrożenia planów, zobacz [następne kroki](#next-steps).

## <a name="take-inventory"></a>Spis
Przed Integrowanie aplikacji z usługą Azure AD, jest ważne, aby wiedzieć, gdzie się znajdujesz i gdzie chcesz przejść.  Poniższe pytania mają na celu pomóc myśleć o projekcie integracji aplikacji usługi Azure AD.

### <a name="application-inventory"></a>Spis aplikacji
* Gdzie są wszystkie swoje aplikacje? Kto jest właścicielem je?
* Jakiego rodzaju uwierzytelniania są wymagane w aplikacji
* Kto potrzebuje dostępu do aplikacji, które?
* Czy chcesz wdrożyć nową aplikację?
  * Będzie ją skompilować wewnętrznych i wdrożyć ją w wystąpieniu obliczeniowym platformy Azure?
  * Będziesz korzystać, taki, który jest dostępny w galerii aplikacji platformy Azure?

### <a name="user-and-group-inventory"></a>Spis użytkowników i grup
* Kont użytkowników lokalizację?
  * Lokalna usługa Active Directory
  * Azure AD
  * W ramach bazy danych innej aplikacji, którego jesteś właścicielem
  * W aplikacjach niezaakceptowane oficjalnie
  * Wszystkie powyższe
* Jakie uprawnienia i przypisań ról pojedynczych użytkowników aktualnie masz? Należy przejrzeć ich dostęp lub czy na pewno, że przypisania dostępu i roli użytkownika są teraz odpowiednie?
* Grupy już istnieją w usłudze Active Directory w środowisku lokalnym?
  * Sposób organizowania grup
  * Którzy są członkami grupy?
  * Jakie przypisania uprawnień/ról czy aktualnie są grupy?
* Będą potrzebne wyczyścić użytkownika/grupę baz danych przed integracji?  (Jest to bardzo ważne pytania. Odzyskiwanie w pamięci w poziomie).

### <a name="access-management-inventory"></a>Spis zarządzania dostęp
* Jak można obecnie zarządzać dostępem użytkowników do aplikacji? Czy, które należy zmienić?  Bierzesz pod uwagę inne sposoby zarządzanie dostępem, takie jak za pomocą [RBAC](../../role-based-access-control/role-assignments-portal.md) na przykład?
* Kto potrzebuje dostępu do co?

Być może w przypadku braku odpowiedzi na wszystkie pytania na początku, ale jest to OK.  Ten przewodnik może pomóc w odpowiedzi na niektóre z tych pytań, a niektóre podejmowaniu świadomych wyborów.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Wyszukaj aplikacje niezaakceptowane oficjalnie chmury z rozwiązaniem Cloud Discovery

Jak wspomniano powyżej, może to być aplikacje, które jeszcze nie zostało zarządzane przez Twoją organizację, do chwili obecnej.  W ramach procesu spisu istnieje możliwość znaleźć aplikacje niezaakceptowane oficjalnie chmury. Zobacz [konfigurowanie rozwiązania Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrowanie aplikacji z usługą Azure AD
Następujące artykuły omówiono różne sposoby aplikacji integracji z usługą Azure AD i zapewnia wskazówki.

* [Określanie, które usługi Active Directory do użycia](../fundamentals/active-directory-administer.md)
* [Za pomocą aplikacji w galerii aplikacji platformy Azure](what-is-single-sign-on.md)
* [Integrowanie listę samouczki aplikacji SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Typy uwierzytelniania
Poszczególnych aplikacji mogą mieć różne uwierzytelniania wymagania. Za pomocą usługi Azure AD certyfikaty podpisywania może służyć za pomocą aplikacji, które używają protokołu SAML 2.0, WS-Federation, lub OpenID Connect protokołów oraz hasło logowania jednokrotnego. Aby uzyskać więcej informacji na temat aplikacji Zobacz typy uwierzytelniania do użycia z usługą Azure AD [zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) i [logowanie na podstawie hasła](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Włączanie logowania jednokrotnego przy użyciu serwera Proxy aplikacji usługi Azure AD
Dzięki serwerowi Proxy aplikacji usługi AD Azure firmy Microsoft można zapewnić dostęp do aplikacji znajdujących się w sieci prywatnej bezpiecznie z dowolnego miejsca i na dowolnym urządzeniu. Po zainstalowaniu łącznika serwera proxy aplikacji w danym środowisku, można je łatwo konfigurować za pomocą usługi Azure AD.

### <a name="integrating-custom-applications"></a>Integrowanie aplikacji niestandardowych
Jeśli piszesz nowej aplikacji i chcesz, aby pomóc deweloperom w wykorzystaniu możliwości usługi Azure AD, zobacz [deweloperów Guiding](../active-directory-applications-guiding-developers-for-lob-applications.md).

Jeśli chcesz dodać niestandardową aplikację do galerii aplikacji Azure, zobacz ["Przynieś własną aplikację" za pomocą konfiguracji usługi Azure AD Self-Service SAML](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
Następujące artykuły opis sposobów zarządzać dostępem do aplikacji po zostały zintegrowane za pomocą usługi Azure AD przy użyciu łączników usługi Azure AD i Azure AD.

* [Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD](what-is-access-management.md)
* [Automatyzacja za pomocą łączników usługi Azure AD](user-provisioning.md)
* [Assigning users to an application](../active-directory-applications-guiding-developers-assigning-users.md) (Przypisywanie użytkowników do aplikacji)
* [Assigning groups to an application](../active-directory-applications-guiding-developers-assigning-groups.md) (Przypisywanie grup do aplikacji)
* [Udostępnianie kont](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje, można pobrać planów wdrożenia usługi Azure Active Directory z [GitHub](https://aka.ms/deploymentplans). Galeria aplikacji, można pobrać planów wdrożenia logowania jednokrotnego dostępu warunkowego i aprowizacji za pośrednictwem użytkowników [witryny Azure portal](https://portal.azure.com). 

Aby pobrać planu wdrożenia w witrynie Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **aplikacje dla przedsiębiorstw** | **wybierz aplikację** | **planu wdrożenia**.

Prześlij opinię na temat planów wdrożenia, wykonując [wdrożenia planu badań](https://aka.ms/DeploymentPlanFeedback).
