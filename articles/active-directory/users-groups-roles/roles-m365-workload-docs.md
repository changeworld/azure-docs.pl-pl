---
title: Obsah role administratora dla obciążeń Microsoft 365 — usługa Azure AD | Dokumentacja firmy Microsoft
description: Znajdź zawartość i dokumentacja interfejsu API dla ról administratora dla obciążeń Microsoft 365 w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7460c3a7d957634d14186d313994c65d89fa4a32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60468185"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Role administratorów dla obciążeń Microsoft 365

Wszystkie produkty w usłudze Microsoft 365, można zarządzać za pomocą ról administracyjnych w usłudze Azure AD. Niektóre produkty także podać dodatkowe role, które są specyficzne dla danego produktu. Aby uzyskać informacji na temat ról obsługiwanych przez każdy produkt zobacz w poniższej tabeli. Ogólne dyskusje na temat delegowania problemów można znaleźć w [delegacja roli planowania w usłudze Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Gdzie można znaleźć zawartości

Microsoft 365 obciążenia | Obsah role | Zawartość interfejsu API
---------------------- | ------------------ | -----------------
Ról administratora w planach usług Office 365 i Microsoft 365 | [Role administratora usługi Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niedostępne
Azure Active Directory (Azure AD) i Azure AD Identity Protection| [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Kontrola dostępu oparta na rolach programu Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Program PowerShell dla programu Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobranie przypisania roli](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role administratora usługi Azure AD](directory-assign-admin-roles.md)<br>Również [rolę administratora dotyczące programu SharePoint w usłudze Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Zespoły/Skype dla firm | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centrum zabezpieczeń i zgodności (Office 365 zaawansowaną ochronę przed zagrożeniami ochrony, Exchange Online Protection Information) | [Role administratora usługi Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [PowerShell programu Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobranie przypisania roli](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Zabezpieczanie wynik | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Menedżer zgodności | [Role menedżera zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niedostępne
Azure Information Protection | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Dokumentacja interfejsu API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Grupy ról usługi Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Niedostępne
Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender | [Kontrola dostępu oparta na rolach usługi Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niedostępne
Privileged Identity Management | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Usługa Intune | [Kontrola dostępu oparta na rolach usługi Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Zarządzanych aplikacji pulpitu | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Pobranie przypisania roli](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Kolejne kroki

* [Jak przypisać lub usunąć role administratora usługi Azure AD](directory-manage-roles-portal.md)
* [Odwołanie role administratora usługi Azure AD](directory-assign-admin-roles.md)
