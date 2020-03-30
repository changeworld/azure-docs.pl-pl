---
title: Wprowadzenie do integracji usługi Azure AD z aplikacjami | Dokumenty firmy Microsoft
description: Ten artykuł jest przewodnikiem o rozpoczęciu integracji usługi Azure Active Directory (AD) z aplikacjami lokalnymi i aplikacjami w chmurze.
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
ms.openlocfilehash: 89b16a8479f8975d101b8a4e26dcb1885d9730bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063384"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integracja usługi Azure Active Directory z przewodnikiem o rozpoczęciu pracy aplikacji

W tym temacie podsumowano proces integrowania aplikacji z usługą Azure Active Directory (AD). Każda z poniższych sekcji zawiera krótkie podsumowanie bardziej szczegółowego tematu, dzięki czemu można określić, które części tego przewodnika wprowadzenie są odpowiednie dla Ciebie.

Aby pobrać szczegółowe plany wdrażania, zobacz [Następne kroki](#next-steps).

## <a name="take-inventory"></a>Weź udział w inwentaryzacji
Przed integrowanie aplikacji z usługi Azure AD, ważne jest, aby wiedzieć, gdzie jesteś i gdzie chcesz się udać.  Poniższe pytania mają na celu pomóc w myśleniu o projekcie integracji aplikacji usługi Azure AD.

### <a name="application-inventory"></a>Spis aplikacji
* Gdzie są wszystkie twoje aplikacje? Kto jest ich właścicielem?
* Jakiego rodzaju uwierzytelniania wymagają twoje aplikacje?
* Kto potrzebuje dostępu do jakich aplikacji?
* Czy chcesz wdrożyć nową aplikację?
  * Czy skompilujesz go we w domu i wdrożysz go w wystąpieniu obliczeniowym platformy Azure?
  * Czy użyjesz jednego, który jest dostępny w galerii aplikacji platformy Azure?

### <a name="user-and-group-inventory"></a>Zasoby reklamowe użytkowników i grup
* Gdzie znajdują się konta użytkowników?
  * Lokalna usługa Active Directory
  * Azure AD
  * W osobnej bazie danych aplikacji, której jesteś właścicielem
  * W niesankcjonowanych aplikacjach
  * Wszystkie powyższe
* Jakie uprawnienia i przypisania ról mają obecnie poszczególni użytkownicy? Czy musisz przejrzeć ich dostęp lub czy masz pewność, że dostęp użytkowników i przypisania ról są teraz odpowiednie?
* Czy grupy są już ustanowione w lokalnej usłudze Active Directory?
  * W jaki sposób organizowane są grupy?
  * Kim są członkowie grupy?
  * Jakie uprawnienia/przypisania ról mają obecnie grupy?
* Czy przed integracją należy wyczyścić bazy danych użytkowników/grup?  (To bardzo ważne pytanie. Śmieci, śmieci na zewnątrz.)

### <a name="access-management-inventory"></a>Zapasy zarządzania dostępem
* Jak obecnie zarządzasz dostępem użytkowników do aplikacji? Czy to musi się zmienić?  Czy rozważałeś inne sposoby zarządzania dostępem, takie jak na przykład [RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Kto potrzebuje dostępu do czego?

Może nie masz odpowiedzi na wszystkie te pytania z góry, ale to jest w porządku.  Ten przewodnik pomoże Ci odpowiedzieć na niektóre z tych pytań i podjąć pewne świadome decyzje.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Znajdowanie nieusankcjonowanych aplikacji w chmurze dzięki odnajdowaniu w chmurze

Jak wspomniano powyżej, mogą istnieć aplikacje, które do tej pory nie były zarządzane przez twoją organizację.  W ramach procesu inwentaryzacji można znaleźć nieusankcjonowane aplikacje w chmurze. Zobacz [Konfigurowanie odnajdywania chmury](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrowanie aplikacji z usługą Azure AD
W poniższych artykułach omówiono różne sposoby integracji aplikacji z usługą Azure AD i zawierają pewne wskazówki.

* [Określanie, którego usługi Active Directory użyć](../fundamentals/active-directory-administer.md)
* [Korzystanie z aplikacji w galerii aplikacji platformy Azure](what-is-single-sign-on.md)
* [Integracja listy samouczków aplikacji SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Typy uwierzytelniania
Każda z aplikacji może mieć inne wymagania uwierzytelniania. W usłudze Azure AD certyfikaty podpisywania mogą być używane z aplikacjami korzystającymi z protokołów SAML 2.0, WS-Federation lub OpenID Connect, a także z logowaniem jednokrotnym haseł. Aby uzyskać więcej informacji na temat typów uwierzytelniania aplikacji do użycia z usługą Azure AD, zobacz [Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) i [logowanie jednookrotne oparte na hasłach](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Włączanie funkcji sytuujących za pomocą serwera proxy aplikacji usługi Azure AD
Za pomocą serwera proxy aplikacji usługi Microsoft Azure AD można zapewnić dostęp do aplikacji znajdujących się w sieci prywatnej bezpiecznie, z dowolnego miejsca i na dowolnym urządzeniu. Po zainstalowaniu łącznika serwera proxy aplikacji w danym środowisku można go łatwo skonfigurować za pomocą usługi Azure AD.

### <a name="integrating-custom-applications"></a>Integracja aplikacji niestandardowych
Jeśli piszesz nową aplikację i chcesz pomóc deweloperom w wykorzystaniu możliwości usługi Azure AD, zobacz [Przewodnicy deweloperzy.](../active-directory-applications-guiding-developers-for-lob-applications.md)

Jeśli chcesz dodać aplikację niestandardową do Galerii aplikacji platformy Azure, zobacz ["Przynieś własną aplikację" z konfiguracją saml samoobsługowej usługi Azure AD.](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
W poniższych artykułach opisano sposoby zarządzania dostępem do aplikacji po ich zintegrowaniu z usługą Azure AD przy użyciu łączników usługi Azure AD i usługi Azure AD.

* [Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD](what-is-access-management.md)
* [Automatyzacja za pomocą łączników usługi Azure AD](../app-provisioning/user-provisioning.md)
* [Assigning users to an application](../active-directory-applications-guiding-developers-assigning-users.md) (Przypisywanie użytkowników do aplikacji)
* [Assigning groups to an application](../active-directory-applications-guiding-developers-assigning-groups.md) (Przypisywanie grup do aplikacji)
* [Udostępnianie kont](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje, można pobrać plany wdrażania usługi Azure Active Directory z [usługi GitHub](https://aka.ms/deploymentplans). W przypadku aplikacji galerii można pobrać plany wdrażania logowania jednokrotnego, dostępu warunkowego i inicjowania obsługi administracyjnej użytkowników za pośrednictwem [witryny Azure portal](https://portal.azure.com). 

Aby pobrać plan wdrożenia z witryny Azure portal:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Aplikacje przedsiębiorstwa** | Wybierz**plan wdrażania****aplikacji** | .

Prześlij opinię na temat planów wdrożenia, korzystając z [ankiety planu wdrożenia.](https://aka.ms/DeploymentPlanFeedback)
