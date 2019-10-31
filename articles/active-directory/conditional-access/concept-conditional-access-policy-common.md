---
title: Typowe zasady dostępu warunkowego — Azure Active Directory
description: Powszechnie używane zasady dostępu warunkowego dla organizacji
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1991a3fe844b3a8669a67e491359e1a96658c844
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151255"
---
# <a name="common-conditional-access-policies"></a>Typowe zasady dostępu warunkowego

Zasady ochrony linii bazowej są doskonałe, ale wiele organizacji wymaga większej elastyczności niż oferta. Na przykład w wielu organizacjach wymagana jest możliwość wykluczenia określonych kont, takich jak ich dostęp awaryjny lub konta administracyjne w przypadku awarii z zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego. W przypadku tych organizacji typowe zasady, do których odwołuje się ten artykuł, mogą być używane.

![Zasady dostępu warunkowego w Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Więcej informacji o kontach dostępu awaryjnego i o tym, dlaczego są ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Utwórz odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typowe zasady wdrożone przez organizacje

* [Wymagaj uwierzytelniania wieloskładnikowego dla administratorów](howto-conditional-access-policy-admin-mfa.md)
* [Wymagaj uwierzytelniania wieloskładnikowego na potrzeby zarządzania na platformie Azure](howto-conditional-access-policy-azure-management.md)
* [Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](howto-conditional-access-policy-all-users-mfa.md)
* [Blokuj starsze uwierzytelnianie](howto-conditional-access-policy-block-legacy.md)
* [Dostęp warunkowy oparty na ryzyku (wymaga Azure AD — wersja Premium P2)](howto-conditional-access-policy-risk.md)
* [Wymagaj zaufanej lokalizacji rejestracji usługi MFA](howto-conditional-access-policy-registration.md)
* [Blokuj dostęp według lokalizacji](howto-conditional-access-policy-location.md)
* [Wymagaj zgodnego urządzenia](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Następne kroki

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
