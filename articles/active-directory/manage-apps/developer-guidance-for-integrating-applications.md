---
title: Zarejestruj aplikację, aby korzystać z usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Ten artykuł napisany dla działu IT Pro zawiera wskazówki dotyczące integrowania aplikacji platformy Azure z usługą Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108282"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Tworzenie aplikacji biznesowych dla usługi Azure Active Directory
Ten przewodnik zawiera omówienie tworzenia aplikacji biznesowych (LoB) dla usługi Azure Active Directory (AD). Docelową grupą odbiorców są administratorzy globalni usługi Active Directory/Office 365.

## <a name="overview"></a>Omówienie
Tworzenie aplikacji zintegrowanych z usługą Azure AD umożliwia użytkownikom w organizacji logowanie jednokrotne za pomocą usługi Office 365. Posiadanie aplikacji w usłudze Azure AD zapewnia kontrolę nad zasadami uwierzytelniania dla aplikacji. Aby dowiedzieć się więcej o dostępie warunkowym i sposobie ochrony aplikacji za pomocą uwierzytelniania wieloskładnikowego (MFA) zobacz [Konfigurowanie reguł dostępu](../conditional-access/app-based-mfa.md).

Zarejestruj aplikację, aby korzystać z usługi Azure Active Directory. Rejestrowanie aplikacji oznacza, że deweloperzy mogą używać usługi Azure AD do uwierzytelniania użytkowników i żądania dostępu do zasobów użytkownika, takich jak poczta e-mail, kalendarz i dokumenty.

Każdy członek katalogu (nie goście) może zarejestrować aplikację, inaczej *nazywaną tworzeniem obiektu aplikacji*.

Rejestracja aplikacji umożliwia każdemu użytkownikowi następujące czynności:

* Uzyskaj tożsamość aplikacji rozpoznawanej przez usługę Azure AD
* Pobierz jeden lub więcej kluczy/kluczy, których aplikacja może użyć do uwierzytelnienia się w u. AD
* Oznacz aplikację w witrynie Azure portal za pomocą niestandardowej nazwy, logo itp.
* Zastosuj funkcje autoryzacji usługi Azure AD do swojej aplikacji, w tym:

  * Kontrola dostępu oparta na rolach (RBAC)
  * Usługa Azure Active Directory jako serwer autoryzacji oAuth (zabezpiecz interfejs API udostępniany przez aplikację)
* Deklarowanie wymaganych uprawnień niezbędnych do działania aplikacji zgodnie z oczekiwaniami, w tym:

     - Uprawnienia aplikacji (tylko administratorzy globalni). Na przykład: członkostwo roli w innej aplikacji usługi Azure AD lub członkostwa roli względem zasobu, grupy zasobów lub subskrypcji platformy Azure
     - Uprawnienia delegowane (dowolny użytkownik). Na przykład: usługa Azure AD, logowanie i profil odczytu

> [!NOTE]
> Domyślnie każdy członek może zarejestrować aplikację. Aby dowiedzieć się, jak ograniczyć uprawnienia do rejestrowania aplikacji do określonych członków, zobacz [Jak aplikacje są dodawane do usługi Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Oto, co ty, administrator globalny, musisz zrobić, aby pomóc deweloperom przygotować aplikację do produkcji:

* Konfigurowanie reguł dostępu (zasady dostępu/usługi MFA)
* Konfigurowanie aplikacji tak, aby wymagała przypisania użytkownika, i przypisywanie użytkowników
* Pomijanie domyślnego środowiska zgody użytkownika

## <a name="configure-access-rules"></a>Konfigurowanie reguł dostępu
Konfigurowanie reguł dostępu dla aplikacji dla aplikacji SaaS. Na przykład można wymagać usługi MFA lub zezwalać tylko na dostęp do użytkowników w zaufanych sieciach. Szczegóły dotyczące tego są dostępne w dokumencie [Konfigurowanie reguł dostępu](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurowanie aplikacji tak, aby wymagała przypisania użytkownika, i przypisywanie użytkowników
Domyślnie użytkownicy mogą uzyskiwać dostęp do aplikacji bez przypisywania. Jeśli jednak aplikacja udostępnia role lub jeśli chcesz, aby aplikacja była wyświetlana w panelu dostępu użytkownika, należy wymagać przypisania użytkownika.

Jeśli jesteś subskrybentem pakietu Azure AD Premium lub Enterprise Mobility Suite (EMS), zdecydowanie zalecamy korzystanie z grup. Przypisywanie grup do aplikacji umożliwia delegowanie bieżącego zarządzania dostępem do właściciela grupy. Można utworzyć grupę lub poprosić odpowiedzialną stronę w organizacji o utworzenie grupy przy użyciu funkcji zarządzania grupą.

[Przypisywanie użytkowników i grup do aplikacji](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Pomijanie zgody użytkownika
Domyślnie każdy użytkownik przechodzi przez środowisko zgody, aby się zalogować. Środowisko zgody, prosząc użytkowników o przyznanie uprawnień do aplikacji, może być niepokojące dla użytkowników, którzy nie są zaznajomieni z podejmowaniem takich decyzji.

W przypadku zaufanych aplikacji można uprościć środowisko użytkownika, wyrażając zgodę na aplikację w imieniu organizacji.

Aby uzyskać więcej informacji na temat zgody użytkownika i środowiska zgody na platformie Azure, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Powiązane artykuły
* [Włącz bezpieczny dostęp zdalny do aplikacji lokalnych za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy.md)
* [Zarządzanie dostępem do aplikacji za pomocą usługi Azure AD](what-is-access-management.md)

