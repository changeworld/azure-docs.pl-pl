---
title: Porównaj przyłączanie do usługi Azure AD i Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Wybieranie między Azure AD Join a usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: ergreenl
ms.openlocfilehash: d4f50ea89f2623d387fb77acb09e609def547468
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359436"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Wybieranie między przyłączanie do usługi Azure Active Directory i Azure Active Directory Domain Services
W tym artykule opisano różnice między dołączania usługi Azure Active Directory (AD) i Azure AD Domain Services i pomoże wybrać, w oparciu o przypadki użycia.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Zarejestrowane w usłudze Azure AD i urządzeń przyłączonych do usługi Azure AD
Usługa Azure AD umożliwia zarządzanie tożsamościami urządzeń używany w Twojej organizacji i kontroli dostępu do zasobów firmy z tych urządzeń. Użytkownicy mogą rejestrować swoich urządzeń osobistych (bring your own) z usługą Azure AD, która inicjuje obsługę urządzenia przy użyciu tożsamości. Później usługi Azure AD można uwierzytelnić urządzenia, gdy użytkownik loguje się do usługi Azure AD i korzysta z urządzenia do dostępu do zabezpieczonych zasobów. Ponadto można zarządzać urządzeniem przy użyciu oprogramowania zarządzania urządzeniami przenośnymi (MDM), takiego jak Microsoft Intune. Ta funkcja pozwala ograniczyć dostęp do poufnych zasobów z urządzenia zarządzane i zgodne z zasadami.

Można również dołączyć urządzenia do usługi Azure AD należące do organizacji. Ten mechanizm zapewnia te same korzyści rejestrowanie osobistych urządzeń z usługą Azure AD. Ponadto użytkownicy mogą logować się do urządzenia przy użyciu swoich poświadczeń firmowych. Urządzenia usługi Azure AD przyłączone zapewniają następujące korzyści:
* Logowanie jednokrotne (SSO) do aplikacji zabezpieczonej przez usługi Azure AD
* Zgodne z zasadami mobilny dostęp firmowy do ustawień użytkownika na urządzeniach.
* Dostęp do Windows Store dla firm przy użyciu poświadczeń firmowych.
* Windows Hello dla firm
* Ograniczony dostęp do aplikacji i zasobów z urządzeń, które są zgodne z zasadami firmowymi.

| **Typ urządzenia** | **Platformy urządzeń** | **Mechanizm** |
|:---| --- | --- |
| Urządzenia osobiste | Windows 10, iOS, Android, Mac OS | Zarejestrowana z usługi Azure AD |
| Organizacja urządzeń, które nie są przyłączone do lokalnej usługi AD | Windows 10 | Dołączono do usługi Azure AD |
| Organizacja posiadane urządzenia przyłączone do lokalnej usługi AD | Windows 10 | Dołączono do hybrydowej usługi Azure AD |

W usłudze Azure AD przyłączony lub zarejestrowanych urządzeń, uwierzytelnianie użytkowników się dzieje, za pomocą nowoczesnych protokołów uwierzytelniania OAuth/OpenID Connect, na podstawie. Protokoły te zostały zaprojektowane do pracy w Internecie i sprawdzają się w scenariuszach mobilnych, gdzie użytkownicy uzyskiwać dostęp do zasobów firmy z dowolnego miejsca.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Przyłączanie do domeny do domeny zarządzanej usług domenowych w usłudze Azure AD
Azure AD Domain Services zapewnia domeny zarządzanej usługi AD w usłudze Azure virtual network. Komputery mogą dołączyć do tej domeny zarządzanej za pomocą mechanizmów tradycyjnych przyłączania do domeny. Klienta Windows (Windows 7, Windows 10) i maszyn z systemem Windows Server mogą być przyłączone do domeny zarządzanej. Ponadto można również dołączyć systemu Linux i Mac OS maszyn do domeny zarządzanej. Gdy dołączysz maszynę do domeny usługi AD, użytkownicy mogą logować się do komputera przy użyciu swoich poświadczeń firmowych. Te maszyny, mogą być zarządzane za pomocą zasad grupy, w związku z tym Wymuszanie zgodności z zasadami zabezpieczeń organizacji.

Na komputerze przyłączonym do domeny uwierzytelnianie użytkowników odbywa się przy użyciu protokołów uwierzytelniania NTLM lub Kerberos. Komputerze przyłączonym do domeny wymaga linii wzroku do kontrolerów domeny z domeny zarządzanej, w kolejności, uwierzytelnianie użytkowników działało. W związku z tym, przyłączone do domeny komputera musi być w tej samej sieci wirtualnej jako domeny zarządzanej. Alternatywnie przyłączone do domeny potrzeb maszyny do przyłączania do domeny zarządzanej za pośrednictwem wirtualnej sieci równorzędnej lub połączenie lokacja lokacja sieci VPN/ExpressRoute. W związku z tym ten mechanizm jest doskonałym rozwiązaniem dla urządzeń przenośnych lub łączyć się z zasobami z spoza sieci firmowej.

> [!NOTE]
> Technicznie rzecz biorąc istnieje możliwość dołączania na stacji roboczej klienta w środowisku lokalnym do domeny zarządzanej za pośrednictwem połączenia lokacja lokacja sieci VPN lub usługi ExpressRoute. Jednak dla urządzenia użytkowników końcowych, które firma Microsoft zdecydowanie zalecamy użycie zarejestrowanie urządzenia w usłudze Azure AD (urządzeń osobistych) albo przyłączenie urządzenia do usługi Azure AD (urządzenia firmowe). Ten mechanizm sprawdzi się najlepiej w Internecie oraz umożliwia użytkownikom końcowym pracy z dowolnego miejsca. Azure AD Domain Services to idealne narzędzie do Windows lub Linux serwer wdrożone maszyny wirtualne w sieciach wirtualnych platformy Azure, na których aplikacje są wdrażane.


## <a name="summary---key-differences"></a>Podsumowanie — podstawowe różnice
| **Aspekt** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| Kontrolowane przez urządzenie | Azure AD | Usługa Azure AD Domain Services domeny zarządzanej |
| Reprezentacja w katalogu | Obiekty urządzeń w katalogu usługi Azure AD. | Obiekty komputerów w domenie zarządzanej usługi Katalogowej usługi AAD. |
| Authentication | Protokoły uwierzytelniania OAuth/OpenID Connect, na podstawie | Protokół Kerberos, protokoły NTLM |
| Zarządzanie | Oprogramowania zarządzania urządzeniami przenośnymi (MDM), takiego jak usługa Intune | Zasady grupy |
| Networking | Działa przez internet | Wymaga maszyny w tej samej sieci wirtualnej jako domeny zarządzanej.|
| Doskonale nadaje się do... | Urządzenia przenośnego lub stacjonarnego użytkowników końcowych | Serwer wdrożone maszyny wirtualne na platformie Azure |


## <a name="next-steps"></a>Kolejne kroki
### <a name="learn-more-about-azure-ad-domain-services"></a>Dowiedz się więcej o usłudze Azure AD Domain Services
* [Omówienie usług domenowych Azure AD](active-directory-ds-overview.md)
* [Funkcje](active-directory-ds-features.md)
* [Scenariusze wdrażania](active-directory-ds-scenarios.md)
* [Dowiedz się, jeśli program Azure AD Domain Services odpowiada przypadki użycia](active-directory-ds-comparison.md)
* [Zrozumienie, jak usługi domenowe Azure AD synchronizuje się z katalogiem usługi Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Dowiedz się więcej o usłudze Azure AD Join
* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Wprowadzenie do usług Azure AD Domain Services
* [Włączanie usług domenowych Azure AD przy użyciu witryny Azure portal](active-directory-ds-getting-started.md)
