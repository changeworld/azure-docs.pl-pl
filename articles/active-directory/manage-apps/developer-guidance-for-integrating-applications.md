---
title: Zarejestruj swoją aplikację, aby używać usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Przeznaczony dla specjalistów IT i w tym artykule zawarto wskazówki dotyczące integracji aplikacji platformy Azure z usługą Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: celested
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 341d7667f039a7272a7ad3fec80a445aa95b619d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440236"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Twórz line-of-business apps usługi Azure Active Directory
Ten przewodnik zawiera omówienie tworzenia aplikacji usługi line-of-business (LoB) dla usługi Azure Active Directory (AD). Określonej grupy odbiorców jest administratorów globalnych usługi Active Directory/Office 365.

## <a name="overview"></a>Omówienie
Tworzenie aplikacji zintegrowanych z usługą Azure AD zapewnia użytkownikom w swojej organizacji logowanie jednokrotne z usługą Office 365. Umieszczenie aplikacji w usłudze Azure AD umożliwia kontrolę nad zasady uwierzytelniania dla aplikacji. Aby dowiedzieć się więcej na temat dostępu warunkowego i sposobu ochrony aplikacji za pomocą uwierzytelniania wieloskładnikowego (MFA), zobacz [Konfigurowanie reguł dostępu](../conditional-access/app-based-mfa.md).

Zarejestruj swoją aplikację, aby używać usługi Azure Active Directory. Trwa rejestrowanie aplikacji oznacza, że deweloperzy można użyć usługi Azure AD do uwierzytelniania użytkowników i poprosić o dostęp do zasobów użytkownika, takich jak wiadomości e-mail, kalendarz i dokumentów.

Dowolny element członkowski katalogu (nie gości) może zarejestrować aplikację, znanych także jako *tworzenia obiektu aplikacji*.

Rejestrowanie aplikacji zezwala każdemu użytkownikowi, wykonaj następujące czynności:

* Pobierz tożsamość dla swoich aplikacji, które rozpoznaje usługi Azure AD
* Pobierz jeden lub więcej wpisów tajnych/kluczy używanych przez aplikację do samodzielnego uwierzytelnienia usługi AD
* Marka aplikacji w witrynie Azure portal za pomocą niestandardowej nazwy, logo, itp.
* Stosowanie funkcji autoryzacji usługi Azure AD do swojej aplikacji, w tym:

  * Kontrola dostępu oparta na rolach (RBAC)
  * Usługa Azure Active Directory co serwer autoryzacji oAuth (Zabezpieczanie interfejsu API udostępnianych przez aplikację)
* Zgodnie z oczekiwaniami, włączając, Zadeklaruj wymagane uprawnienia niezbędne do zastosowania do funkcji:

     - Uprawnienia aplikacji (tylko administratorzy globalni). Na przykład: Członkostwo w roli w innej usłudze Azure AD aplikacji lub członkostwa względem zasobu, grupy zasobów platformy Azure, lub subskrypcji
     - Delegowane uprawnienia (każdego użytkownika). Na przykład: Profil usługi Azure AD, logowania i odczytu

> [!NOTE]
> Domyślnie każdy członek może zarejestrować aplikację. Aby dowiedzieć się, jak ograniczyć uprawnienia do rejestrowania aplikacji określonych członków, zobacz [jak aplikacje są dodawane do usługi Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Oto, co, administratora globalnego, należy wykonać, aby pomóc deweloperom przygotowania ich aplikacji w środowisku produkcyjnym:

* Skonfiguruj reguły dostępu (zasady dostępu/MFA)
* Konfigurowanie aplikacji w celu wymaganie przypisania użytkownika i Przypisz użytkowników
* Pomiń domyślne środowisko zgody użytkownika

## <a name="configure-access-rules"></a>Skonfiguruj reguły dostępu
Skonfiguruj reguły dostępu do poszczególnych aplikacji do aplikacji SaaS. Można na przykład wymagać uwierzytelniania Wieloskładnikowego lub zezwalanie na dostęp do użytkowników tylko w sieciach zaufanych. Dla tego są one dostępne w dokumencie [Konfigurowanie reguł dostępu](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurowanie aplikacji w celu wymaganie przypisania użytkownika i Przypisz użytkowników
Domyślnie użytkownicy mają dostęp do aplikacji bez przypisania. Jednakże jeśli aplikacja udostępnia ról lub jeśli chcesz, aby aplikacja była wyświetlana na panelu dostępu użytkownika, należy wymagać przypisania użytkownika.

Jeśli jesteś subskrybentem programu Azure AD Premium lub Enterprise Mobility Suite (EMS), zdecydowanie zaleca się korzystanie z grup. Przypisywanie grup do aplikacji umożliwia delegowanie zarządzania ciągły dostęp do właściciela grupy. Można utworzyć grupę, lub poprosić odpowiada stronę w Twojej organizacji, aby utworzyć grupę przy użyciu funkcji z grupy zarządzania.

[Przypisywanie użytkowników i grup do aplikacji](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Pomiń zgoda użytkownika
Domyślnie każdy użytkownik przechodzi przez środowisko zgody, aby zalogować się. Środowisko zgody, prosząc użytkowników o nadanie uprawnień do aplikacji, może być niejasna dla użytkowników, którzy nie zna podejmowanie tych decyzji.

Dla aplikacji, którym ufasz można uprościć środowisko użytkownika, który integruję aplikację w imieniu swojej organizacji.

Aby uzyskać więcej informacji na temat zgody użytkownika i zgody środowisko na platformie Azure, zobacz [Integrowanie aplikacji w usłudze Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Powiązane artykuły
* [Włączyć bezpieczny dostęp zdalny do aplikacji lokalnych przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy.md)
* [Zarządzanie dostępem do aplikacji za pomocą usługi Azure AD](what-is-access-management.md)

