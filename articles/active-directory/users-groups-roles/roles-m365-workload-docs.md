---
title: Dokumenty roli administratora w usługach Microsoft 365 — Usługa Azure AD | Dokumenty firmy Microsoft
description: Znajdowanie zawartości i odwołań do interfejsu API ról administratora usług usługi Microsoft 365 w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024459"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Role administratora usług usługi Microsoft 365

Wszystkie produkty w usłudze Microsoft 365 można zarządzać za pomocą ról administracyjnych w usłudze Azure AD. Niektóre produkty zapewniają również dodatkowe role, które są specyficzne dla tego produktu. Aby uzyskać informacje na temat ról obsługiwanych przez każdy produkt, zobacz poniższą tabelę. Ogólne dyskusje na temat problemów z delegowaniem można znaleźć w [obszarze Planowanie delegowania ról w usłudze Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Gdzie znaleźć zawartość

Usługa Microsoft 365 | Zawartość roli | Zawartość interfejsu API
---------------------- | ------------------ | -----------------
Role administratora w planach biznesowych usługi Office 365 i Microsoft 365 | [Role administratora usługi Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niedostępne
Usługa Azure Active Directory (Azure AD) i usługa Azure AD Identity Protection| [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Kontrola dostępu oparta na rolach programu Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role administratora usługi Azure AD](directory-assign-admin-roles.md)<br>Również [rola administratora programu SharePoint w usłudze Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) — informacje | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Zespoły/Skype dla firm | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centrum zgodności & zabezpieczeń (zaawansowana ochrona przed zagrożeniami usługi Office 365, ochrona w trybie online, ochrona informacji) | [Role administratora usługi Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Bezpieczny wynik | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Menedżer zgodności | [Role Menedżera zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niedostępne
Azure Information Protection | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Odwołanie do interfejsu API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Grupy ról Zaawansowanej ochrony przed zagrożeniami na platformie Azure](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Niedostępne
Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender | [Kontrola dostępu oparta na rolach usługi Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niedostępne
Privileged Identity Management | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Usługa Intune | [Kontrola dostępu oparta na rolach usługi Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Pulpit zarządzany | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API wykresu](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Następne kroki

* [Jak przypisać lub usunąć role administratora usługi Azure AD](directory-manage-roles-portal.md)
* [Odwołanie do ról administratora usługi Azure AD](directory-assign-admin-roles.md)
