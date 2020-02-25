---
title: Udzielanie kontroli w zasadach dostępu warunkowego — Azure Active Directory
description: Co to jest przyznanie kontroli w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb098363a6f1b27bd8afa8e68ab14bfa666ea539
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561650"
---
# <a name="conditional-access-grant"></a>Dostęp warunkowy: Udziel

W ramach zasad dostępu warunkowego administrator może korzystać z kontroli dostępu w celu udzielenia lub zablokowania dostępu do zasobów.

![Zasady dostępu warunkowego z kontrolką Grant wymagające uwierzytelniania wieloskładnikowego](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokuj dostęp

Zablokowanie uwzględnia wszelkie przypisania i uniemożliwia dostęp na podstawie konfiguracji zasad dostępu warunkowego.

Block to zaawansowana kontrolka, która powinna być wielded z odpowiednią wiedzą. Aby przetestować przed włączeniem, Administratorzy powinni używać [trybu tylko do raportowania](concept-conditional-access-report-only.md) .

## <a name="grant-access"></a>Udzielanie dostępu

Administratorzy mogą zdecydować się na wymuszenie co najmniej jednej kontrolki podczas udzielania dostępu. Te kontrolki obejmują następujące opcje: 

- [Wymagaj uwierzytelniania wieloskładnikowego (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Wymagaj, aby urządzenie było oznaczone jako zgodne (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Wymagaj zatwierdzonej aplikacji klienckiej](app-based-conditional-access.md)
- [Wymagaj zasad ochrony aplikacji](app-protection-based-conditional-access.md)

Gdy administratorzy zdecydują się połączyć te opcje, mogą wybrać następujące metody:

- Wymagaj wszystkich zaznaczonych kontrolek (kontrolka **i** kontrola)
- Wymagaj jednej z wybranych kontrolek (kontrolki **lub** kontrolka)

Domyślnie dostęp warunkowy wymaga wszystkich zaznaczonych kontrolek.

### <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Zaznaczenie tego pola wyboru spowoduje, że użytkownicy będą musieli wykonać Multi-Factor Authentication platformy Azure. Więcej informacji na temat wdrażania usługi Azure Multi-Factor Authentication można znaleźć w artykule [Planowanie wdrożenia usługi azure Multi-Factor Authentication opartego na chmurze](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Organizacje, które wdrożyły Microsoft Intune mogą korzystać z informacji zwróconych z urządzeń w celu identyfikowania urządzeń spełniających określone wymagania w zakresie zgodności. Te informacje o zgodności z tymi zasadami są przekazywane z usługi Intune w usłudze Azure AD, gdzie dostęp warunkowy może podejmować decyzje o udzieleniu lub zablokowaniu dostępu do zasobów. Aby uzyskać więcej informacji na temat zasad zgodności, zobacz artykuł [Ustawianie reguł na urządzeniach w celu zezwalania na dostęp do zasobów w organizacji przy użyciu usługi Intune](https://docs.microsoft.com/intune/protect/device-compliance-get-started).

Urządzenie może być oznaczone jako zgodne przez usługę Intune (dla dowolnego systemu operacyjnego urządzenia) lub przez system MDM innej firmy dla urządzeń z systemem Windows 10. Systemy zarządzania urządzeniami przenośnymi innych firm dla typów systemów operacyjnych innych niż Windows 10 nie są obsługiwane.

Aby można było oznaczyć je jako zgodne, urządzenia muszą być zarejestrowane w usłudze Azure AD. Więcej informacji na temat rejestracji urządzeń można znaleźć w artykule [co to jest tożsamość urządzenia](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD

Organizacje mogą zdecydować się na użycie tożsamości urządzenia jako części zasad dostępu warunkowego. Organizacje mogą wymagać, aby urządzenia były dołączone do hybrydowej usługi Azure AD przy użyciu tego pola wyboru. Aby uzyskać więcej informacji o tożsamościach urządzeń, zobacz artykuł [co to jest tożsamość urządzenia?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Wymagaj zatwierdzonej aplikacji klienckiej

Organizacje mogą wymagać od zatwierdzonej aplikacji klienckiej próby dostępu do wybranych aplikacji w chmurze. Te zatwierdzone punkty dostępu klienta obsługują [Zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) niezależnie od dowolnego rozwiązania do zarządzania urządzeniami przenośnymi (MDM).

To ustawienie dotyczy następujących aplikacji klienckich:

- Microsoft Azure Information Protection
- Rezerwacje firmy Microsoft
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype dla firm
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Uwagi**

- Zatwierdzone aplikacje klienckie obsługują funkcję zarządzania aplikacjami mobilnymi w usłudze Intune.
- Wymagane wymagania dotyczące **zatwierdzonej aplikacji klienckiej** :
   - Obsługuje tylko warunek platformy dla systemów iOS i Android.
- Dostęp warunkowy nie może rozważyć Microsoft Edge w trybie InPrivate w zatwierdzonej aplikacji klienckiej.

### <a name="require-app-protection-policy"></a>Wymaganie zasad ochrony aplikacji

W zasadach dostępu warunkowego można wymagać, aby [Zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) były obecne w aplikacji klienckiej przed udostępnieniem dostępu do wybranych aplikacji w chmurze. 

To ustawienie dotyczy następujących aplikacji klienckich:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Uwagi**

- Aplikacje dla zasad ochrony aplikacji obsługują funkcję zarządzania aplikacjami mobilnymi w usłudze Intune z ochroną zasad.
- Wymagania dotyczące wymagań **zasad ochrony aplikacji** :
    - Obsługuje tylko warunek platformy dla systemów iOS i Android.

### <a name="terms-of-use"></a>Warunki użytkowania

Jeśli Twoja organizacja utworzyła warunki użytkowania, dodatkowe opcje mogą być widoczne w obszarze Udziel kontroli. Te opcje umożliwiają administratorom wymaganie potwierdzenia warunków użytkowania jako stanu dostępu do zasobów chronionych przez zasady. Więcej informacji na temat warunków użytkowania można znaleźć w artykule [Azure Active Directory warunki użytkowania](terms-of-use.md).

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: kontrolki sesji](concept-conditional-access-session.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb tylko do raportowania](concept-conditional-access-report-only.md)
