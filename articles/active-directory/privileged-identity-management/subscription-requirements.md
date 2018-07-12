---
title: Privileged Identity Management subskrypcji — Azure | Dokumentacja firmy Microsoft
description: Wyjaśnia, subskrypcję i wymaganiach dotyczących licencjonowania dla i zarządzanie nimi za pomocą usługi Azure AD Privileged Identity Management w ramach dzierżawy
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 681169fc3db69c2dfb8aa36d6e2896e0ddba8b56
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548132"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Wymagania dotyczące subskrypcji usługi Azure Active Directory Privileged Identity Management

Usługa Azure AD Privileged Identity Management jest dostępna w ramach usługi Azure AD w wersji Premium P2. Aby uzyskać więcej informacji na temat innych funkcji P2 i porównać ją w warstwie Premium P1, zobacz [usługi Azure Active Directory w wersjach](../active-directory-editions.md).

>[!NOTE]
Podczas usługi Azure Active Directory (Azure AD) Privileged Identity Management w wersji zapoznawczej nie wystąpiły żadne testy licencji dla dzierżawcy Wypróbuj tę usługę.  Teraz, gdy publicznym udostępnieniu produktów usługi Azure AD Privileged Identity Management to subskrypcji próbnej lub płatnej musi znajdować się dla dzierżawy kontynuować korzystanie z Privileged Identity Management od grudnia 2016 r.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Potwierdzenie informacji o subskrypcji próbnej lub płatnej

Jeśli nie masz pewności, czy Twoja organizacja ma korzystać z wersji próbnej lub kupić subskrypcję, można sprawdzić czy za pomocą poleceń, objęte usługi Azure Active Directory modułu dla Windows PowerShell w wersji 1 jest subskrypcji w ramach dzierżawy. 
1. Otwórz okno programu PowerShell.
2. Wprowadź `Connect-MsolService` do uwierzytelnienia się jako użytkownik w dzierżawie.
3. Wprowadź `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

To polecenie pobiera listę subskrypcji w ramach dzierżawy. Jeśli nie ma żadnych wierszy, zwrócone, konieczne będzie uzyskania wersji próbnej i komercyjnej usługi Azure AD Premium P2 subskrypcji P2 usługi Azure AD Premium lub EMS E5 subskrypcji do użycia usługi Azure AD Privileged Identity Management.  Aby uzyskać wersję próbną i Rozpocznij korzystanie z usługi Azure AD Privileged Identity Management, przeczytaj [Rozpoczynanie pracy z usługą Azure AD Privileged Identity Management](pim-getting-started.md).

Jeśli to polecenie zwróci wiersz, w które SkuPartNumber jest "AAD_PREMIUM_P2" lub "EMSPREMIUM" i IsTrial jest "True", oznacza to, że wersji próbnej usługi Azure AD Premium P2 znajduje się w dzierżawie.  Jeśli stan subskrypcji nie jest włączona, a nie masz subskrypcji P2 usługi Azure AD Premium lub EMS E5 zakupu, następnie należy zakupić subskrypcję usługi Azure AD Premium P2 lub EMS E5 aby kontynuować korzystanie z usługi Azure AD Privileged Identity Management.

Usługa Azure AD Premium P2 jest dostępna za pośrednictwem [umowy Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [programu licencjonowania zbiorowego Open](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)i [programu dostawców rozwiązań w chmurze](https://partner.microsoft.com/en-US/cloud-solution-provider). Subskrybenci platformy Azure i usługi Office 365 mogą też kupić usługę Azure AD Premium P2 w trybie online.  Więcej informacji na temat cen usługi Azure AD Premium i jak Zamów w trybie online, można znaleźć w folderze [usługi Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Usługa Azure AD Privileged Identity Management nie jest dostępna w dzierżawie

Azure AD Privileged Identity Management nie będzie już dostępna w dzierżawie, jeśli:
- Twoja organizacja została przy użyciu usługi Azure AD Privileged Identity Management w wersji zapoznawczej i nie kupuje subskrypcję usługi Azure AD Premium P2 lub EMS E5.
- Twoja organizacja ma P2 usługi Azure AD Premium lub EMS E5 wersji próbnej, która wygasła.
- Twoja organizacja ma zakupioną subskrypcję, która wygasła.

Po wygaśnięciu subskrypcji P2 usługi Azure AD Premium lub EMS E5 subskrypcji lub moduł organizacji, która używała usługi Azure AD Privileged Identity Management w wersji zapoznawczej nie uzyskać subskrypcji P2 usługi Azure AD Premium lub EMS E5:

- Będzie to miało wpływu trwałego roli przypisania do ról usługi Azure AD.
- Rozszerzenie usługi Azure AD Privileged Identity Management w witrynie Azure portal oraz poleceń cmdlet interfejsu API programu Graph i interfejsy programu PowerShell usługi Azure AD Privileged Identity Management, nie będzie już użytkownikom aktywację ról uprzywilejowanych, zarządzanie poziomem uprawnień dostęp lub wykonać przeglądów ról uprzywilejowanych.
- Kwalifikujące się przypisania ról ról usługi Azure AD zostanie usunięty, ponieważ użytkownicy nie będą mogły uaktywniać swoje role uprzywilejowane.
- Zakończy się dowolnym bieżących przeglądów ról usługi Azure AD, a ustawienia konfiguracji usługi Azure AD Privileged Identity Management zostanie usunięta.
- Usługa Azure AD Privileged Identity Management nie jest już będzie wysyłać wiadomości e-mail na zmianach przypisania roli.

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie pracy z usługą Azure AD Privileged Identity Management](pim-getting-started.md)
- [Role w usłudze Azure AD Privileged Identity Management](pim-roles.md)
