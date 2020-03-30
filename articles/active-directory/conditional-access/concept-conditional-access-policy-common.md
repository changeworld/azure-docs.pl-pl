---
title: Typowe zasady dostępu warunkowego — usługa Azure Active Directory
description: Powszechnie używane zasady dostępu warunkowego dla organizacji
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295308"
---
# <a name="common-conditional-access-policies"></a>Typowe zasady dostępu warunkowego

[Domyślne zabezpieczenia](../fundamentals/concept-fundamentals-security-defaults.md) są idealne dla niektórych, ale wiele organizacji potrzebuje większej elastyczności niż oferują. Na przykład wiele osób potrzebuje możliwości wykluczenia określonych kont, takich jak ich konta dostępu awaryjnego lub konta administracyjne typu break-glass, z zasad dostępu warunkowego wymagających uwierzytelniania wieloskładnikowego. W przypadku tych organizacji wspólne zasady, do których odwołuje się ten artykuł, mogą być używane.

![Zasady dostępu warunkowego w witrynie Azure portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Więcej informacji na temat kont dostępu awaryjnego i powodów, dla których są one ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Tworzenie strategii zarządzania elastyczną kontrolą dostępu za pomocą usługi Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typowe zasady wdrażane przez organizacje

* [Blokowanie uwierzytelniania starszego](howto-conditional-access-policy-block-legacy.md)\*
* [Wymagaj usługi MFA dla administratorów](howto-conditional-access-policy-admin-mfa.md)\*
* [Wymagaj usługi MFA do zarządzania platformą Azure](howto-conditional-access-policy-azure-management.md)\*
* [Wymagaj usługi MFA dla wszystkich użytkowników](howto-conditional-access-policy-all-users-mfa.md)\*

\*Te cztery zasady po skonfigurowaniu razem, będzie naśladować funkcje włączone przez [domyślne zabezpieczenia](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Dodatkowe zasady

* [Dostęp warunkowy oparty na ryzyku (wymaga usługi Azure AD w warstwie Premium P2)](howto-conditional-access-policy-risk.md)
* [Wymaganie zaufanej lokalizacji na potrzeby rejestracji uwierzytelniania wieloskładnikowego](howto-conditional-access-policy-registration.md)
* [Blokowanie dostępu według lokalizacji](howto-conditional-access-policy-location.md)
* [Wymaganie zgodnego urządzenia](howto-conditional-access-policy-compliant-device.md)
* [Blokowanie dostępu z wyjątkiem określonych aplikacji](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Następne kroki

- [Symuluj zachowanie logowania za pomocą narzędzia Co zrobić w przypadku dostępu warunkowego.](troubleshoot-conditional-access-what-if.md)

- [Użyj trybu tylko do raportu dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)
