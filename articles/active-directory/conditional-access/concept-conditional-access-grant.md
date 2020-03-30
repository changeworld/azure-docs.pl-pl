---
title: Kontrolki udzielania dotacji w zasadach dostępu warunkowego — usługa Azure Active Directory
description: Co to są formanty dotacji w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331837"
---
# <a name="conditional-access-grant"></a>Dostęp warunkowy: Dotacja

W ramach zasad dostępu warunkowego administrator może korzystać z kontroli dostępu, aby udzielić lub zablokować dostęp do zasobów.

![Zasady dostępu warunkowego z kontrolą dotacji wymagającą uwierzytelniania wieloskładnikowego](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokowanie dostępu

Blok uwzględnia wszelkie przypisania i uniemożliwia dostęp na podstawie konfiguracji zasad dostępu warunkowego.

Blok jest potężną kontrolą, która powinna być dzierżona z odpowiednią wiedzą. Jest to coś, czego administratorzy powinni używać trybu tylko do [raportu,](concept-conditional-access-report-only.md) aby przetestować przed włączeniem.

## <a name="grant-access"></a>Udzielanie dostępu

Administratorzy mogą wymusić jeden lub więcej formantów podczas udzielania dostępu. Te formanty obejmują następujące opcje: 

- [Wymagaj uwierzytelniania wieloskładnikowego (uwierzytelniania wieloskładnikowego azure)](../authentication/concept-mfa-howitworks.md)
- [Wymagaj, aby urządzenie było oznaczone jako zgodne (usługa Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Wymagaj hybrydowego urządzenia przyłączającego do usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Wymagaj zatwierdzonej aplikacji klienckiej](app-based-conditional-access.md)
- [Wymaganie zasad ochrony aplikacji](app-protection-based-conditional-access.md)

Gdy administratorzy zdecydują się połączyć te opcje, mogą wybrać następujące metody:

- Wymagaj wszystkich wybranych elementów sterujących (formantu **i** sterowania)
- Wymagaj jednego z wybranych formantów (formantu **OR)**

Domyślnie dostęp warunkowy wymaga wszystkich wybranych formantów.

### <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Zaznaczenie tego pola wyboru będzie wymagało od użytkowników wykonywania uwierzytelniania wieloskładnikowego platformy Azure. Więcej informacji na temat wdrażania uwierzytelniania wieloskładnikowego platformy Azure można znaleźć w artykule [Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure w chmurze.](../authentication/howto-mfa-getstarted.md)

### <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Organizacje, które wdrożyły usługę Microsoft Intune, mogą używać informacji zwróconych z ich urządzeń do identyfikowania urządzeń spełniających określone wymagania dotyczące zgodności. Te informacje o zgodności zasad są przekazywane z usługi Intune do usługi Azure AD, gdzie dostęp warunkowy może podejmować decyzje o przyznaniu lub zablokowaniu dostępu do zasobów. Aby uzyskać więcej informacji na temat zasad zgodności, zobacz artykuł [Ustawianie reguł na urządzeniach w celu umożliwienia dostępu do zasobów w organizacji przy użyciu usługi Intune](/intune/protect/device-compliance-get-started).

Urządzenie może być oznaczone jako zgodne przez usługę Intune (dla dowolnego systemu operacyjnego urządzenia) lub przez system MDM innej firmy dla urządzeń z systemem Windows 10. Jamf pro jest jedynym obsługiwanym systemem MDM innych firm. Więcej informacji na temat integracji można znaleźć w artykule [Integruj Jamf Pro z intune w celu zapewnienia zgodności](/intune/protect/conditional-access-integrate-jamf).

Urządzenia muszą być zarejestrowane w usłudze Azure AD, zanim będą mogły być oznaczone jako zgodne. Więcej informacji na temat rejestracji urządzenia można znaleźć w artykule, [Co to jest tożsamość urządzenia](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Wymagaj hybrydowego urządzenia przyłączającego do usługi Azure AD

Organizacje mogą używać tożsamości urządzenia jako części zasad dostępu warunkowego. Organizacje mogą wymagać, aby urządzenia były hybrydowe usługi Azure AD przyłączone przy użyciu tego pola wyboru. Aby uzyskać więcej informacji na temat tożsamości urządzeń, zobacz artykuł [Co to jest tożsamość urządzenia?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Wymaganie zatwierdzonej aplikacji klienckiej

Organizacje mogą wymagać, aby próba dostępu do wybranych aplikacji w chmurze została wykonana z zatwierdzonej aplikacji klienckiej. Te zatwierdzone aplikacje klienckie obsługują [zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) niezależnie od rozwiązania do zarządzania urządzeniami przenośnymi (MDM).

Aby wykorzystać tę kontrolę dotacji, dostęp warunkowy wymaga, aby urządzenie było zarejestrowane w usłudze Azure Active Directory, która wymaga użycia aplikacji brokera. Aplikacją brokera jest aplikacja Microsoft Authenticator w przypadku systemu iOS lub aplikacja Portal firmy Microsoft w przypadku urządzeń z systemem Android. Jeśli aplikacja brokera nie jest zainstalowana na urządzeniu, gdy użytkownik próbuje uwierzytelnić, użytkownik zostanie przekierowany do sklepu z aplikacjami, aby zainstalować aplikację brokera.

To ustawienie dotyczy następujących aplikacji na iOS i Android:

- Ochrona informacji platformy Microsoft Azure
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Centrum pakietu Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Program Microsoft Outlook
- Planista firmy Microsoft
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype dla firm
- Microsoft StaffHub
- Usługa Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**Uwagi**

- Zatwierdzone aplikacje klienckie obsługują funkcję zarządzania aplikacjami mobilnymi usługi Intune.
- Wymagaj **wymaganego zatwierdzenia aplikacji klienckiej:**
   - Obsługuje tylko stan platformy iOS i Android dla urządzeń.
   - Aplikacja brokera jest wymagana do zarejestrowania urządzenia. W systemie iOS aplikacja brokera jest Microsoft Authenticator i na Androida, jest to aplikacja Intune Company Portal.
- Dostęp warunkowy nie może uznać przeglądarki Microsoft Edge w trybie InPrivate za zatwierdzoną aplikację kliencką.

Zobacz artykuł [Jak: Wymagaj zatwierdzonych aplikacji klienckich do dostępu do aplikacji w chmurze z dostępem warunkowym dla](app-based-conditional-access.md) przykładów konfiguracji.

### <a name="require-app-protection-policy"></a>Wymaganie zasad ochrony aplikacji

W zasadach dostępu warunkowego można wymagać, aby [zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) były obecne w aplikacji klienckiej, zanim dostęp do wybranych aplikacji w chmurze będzie dostępny. 

Aby wykorzystać tę kontrolę dotacji, dostęp warunkowy wymaga, aby urządzenie było zarejestrowane w usłudze Azure Active Directory, która wymaga użycia aplikacji brokera. Aplikacją brokera jest aplikacja Microsoft Authenticator w przypadku systemu iOS lub aplikacja Portal firmy Microsoft w przypadku urządzeń z systemem Android. Jeśli aplikacja brokera nie jest zainstalowana na urządzeniu, gdy użytkownik próbuje uwierzytelnić, użytkownik zostanie przekierowany do sklepu z aplikacjami, aby zainstalować aplikację brokera.

To ustawienie dotyczy następujących aplikacji klienckich:

- Microsoft Cortana
- Microsoft OneDrive
- Program Microsoft Outlook
- Planista firmy Microsoft

**Uwagi**

- Zasady ochrony aplikacji obsługują funkcję zarządzania aplikacjami mobilnymi usługi Intune z ochroną zasad.
- Wymagania **zasad ochrony aplikacji Wymagaj:**
    - Obsługuje tylko stan platformy iOS i Android dla urządzeń.
    - Aplikacja brokera jest wymagana do zarejestrowania urządzenia. W systemie iOS aplikacja brokera jest Microsoft Authenticator i na Androida, jest to aplikacja Intune Company Portal.

Zobacz artykuł [Jak: Wymagaj zasad ochrony aplikacji i zatwierdzonej aplikacji klienckiej do dostępu do aplikacji w chmurze z dostępem warunkowym dla](app-protection-based-conditional-access.md) przykładów konfiguracji.

### <a name="terms-of-use"></a>Warunki użytkowania

Jeśli organizacja utworzyła warunki użytkowania, dodatkowe opcje mogą być widoczne w ramach kontroli dotacji. Te opcje umożliwiają administratorom wymaganie potwierdzenia warunków użytkowania jako warunku dostępu do zasobów chronionych przez zasady. Więcej informacji na temat warunków użytkowania można znaleźć w artykule, [warunki użytkowania usługi Azure Active Directory](terms-of-use.md).

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: kontrolki sesji](concept-conditional-access-session.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb samego raportu](concept-conditional-access-report-only.md)
