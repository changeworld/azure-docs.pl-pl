---
title: Rejestrowanie aplikacji do użycia usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Zapisane dla specjalistów IT, w tym artykule przedstawiono wskazówki dotyczące integracji aplikacji Azure z usługą Active Directory.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: kgremban
ms.custom: seohack1
ms.openlocfilehash: 10517f5684774c47ccc96c773287476c2614613f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292511"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Tworzenie aplikacji biznesowych z usługi Azure Active Directory
Ten przewodnik zawiera omówienie tworzenia — biznesowych (LoB) aplikacji dla usługi Azure Active Directory (AD). Docelowa grupa odbiorców jest Administratorzy globalni usługi Active Directory/Office 365.

## <a name="overview"></a>Przegląd
Tworzenie aplikacji zintegrowanych z usługą Azure AD pozwala użytkownikom w Twojej organizacji logowanie jednokrotne z usługą Office 365. W usłudze Azure AD umożliwia kontrolę nad zasady uwierzytelniania dla aplikacji, których aplikacja. Aby dowiedzieć się więcej na temat dostępu warunkowego i jak chronić aplikacje z uwierzytelnianiem wieloskładnikowym (MFA), zobacz [Konfigurowanie reguł dostępu](active-directory-conditional-access-azure-portal-get-started.md).

Rejestrowanie aplikacji do użycia usługi Azure Active Directory. Trwa rejestrowanie aplikacji oznacza, że deweloperów można używać usługi Azure AD do uwierzytelniania użytkowników i uzyskać dostęp do zasobów użytkownika, takie jak wiadomości e-mail, kalendarza i dokumentów.

Każdy członek katalogu (nie gości) można zarejestrować aplikacji, nazywanego *tworzenia obiektu aplikacji*.

Rejestrowanie aplikacji zezwala każdemu użytkownikowi wykonywać następujące czynności:

* Uzyskać tożsamości dla swoich aplikacji, która rozpoznaje usługi Azure AD
* Pobierz co najmniej jeden haseł/kluczy używanych przez aplikację do samodzielnego uwierzytelnienia w usłudze AD
* Marki aplikacji w portalu Azure z niestandardowej nazwy, logo, itp.
* Stosować funkcje autoryzacji usługi Azure AD do swoich aplikacji, w tym:

  * Kontrola dostępu oparta na rolach (RBAC)
  * Usługa Azure Active Directory jako serwera autoryzacji oAuth (bezpieczna udostępniany przez aplikację interfejsu API)
* Zadeklarować wymagane uprawnienia niezbędne do funkcji aplikacji zgodnie z oczekiwaniami, w tym:

      - Uprawnienia aplikacji (tylko administratorzy globalni). Na przykład: członkostwo roli w innej usłudze Azure AD aplikacji lub członkostwa względem Azure zasobu, grupy zasobów, lub subskrypcji
      - Delegowane uprawnienia (każdy użytkownik). Na przykład: usługi Azure AD, logowania i odczytu profilu

> [!NOTE]
> Domyślnie każdy członek można zarejestrować aplikacji. Aby dowiedzieć się, jak ograniczyć uprawnienia do rejestrowania aplikacji do określonego członka, zobacz [jak aplikacje są dodawane do usługi Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Oto, co, administrator globalny, należy wykonać, aby pomóc deweloperom przygotowania aplikacji do produkcji:

* Konfigurowanie reguł dostępu (zasady dostępu/MFA)
* Konfiguruje aplikację do wymagają przypisania użytkownika i Przypisz użytkowników
* Pomiń domyślne środowisko zgody użytkownika

## <a name="configure-access-rules"></a>Skonfiguruj reguły dostępu
Konfigurowanie reguł dla poszczególnych aplikacji dostępu do aplikacji SaaS. Można na przykład wymagać uwierzytelniania MFA lub tylko zezwolić na dostęp do użytkowników w zaufanych sieciach. Dla tego są one dostępne w dokumencie [Konfigurowanie reguł dostępu](active-directory-conditional-access-azure-portal-get-started.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfiguruje aplikację do wymagają przypisania użytkownika i Przypisz użytkowników
Domyślnie użytkownicy mogą uzyskiwać dostęp do aplikacji bez przypisywane. Jednak aplikacja udostępnia ról lub chcesz aplikacji na panelu dostępu użytkownika, należy włączyć przypisanie użytkownika.

[Requiring user assignment](active-directory-applications-guiding-developers-requiring-user-assignment.md) (Wymaganie przypisania użytkownika)

Jeśli jesteś subskrybentem Azure AD Premium lub Enterprise Mobility Suite (EMS), zdecydowanie zaleca się korzystanie z grup. Przypisywanie grup do aplikacji umożliwia delegowanie zarządzania stałe dostępu do właściciela grupy. Można utworzyć grupy lub poprosić odpowiedzialny stronę w Twojej organizacji, aby utworzyć grupę przy użyciu funkcji z grupy zarządzania.

[Assigning users to an application](active-directory-applications-guiding-developers-assigning-users.md) (Przypisywanie użytkowników do aplikacji)  
[Assigning groups to an application](active-directory-applications-guiding-developers-assigning-groups.md) (Przypisywanie grup do aplikacji)

## <a name="suppress-user-consent"></a>Pomiń zgody użytkownika
Domyślnie każdy użytkownik przechodzi przez środowisko zgody, do logowania. Środowisko zgody, prosząc użytkowników można przyznać uprawnień do aplikacji, może być niejasna dla użytkowników, którzy nie zna podejmowanie tych decyzji.

Dla aplikacji, którym ufasz można uprościć czynności użytkowników przez zgodę aplikacji w imieniu swojej organizacji.

Aby uzyskać więcej informacji o zgodę użytkownika i zgody występują na platformie Azure, zobacz [integracji aplikacji z usługą Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Pokrewne artykuły
* [Włącz bezpieczny dostęp zdalny do aplikacji lokalnych przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy.md)
* [Podgląd warunkowego dostępu na platformie Azure dla aplikacji SaaS](active-directory-conditional-access-azure-portal-get-started.md)
* [Zarządzanie dostępem do aplikacji z usługą Azure AD](manage-apps/what-is-access-management.md)
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
