---
title: Privileged Identity Management subskrypcje - Azure | Dokumentacja firmy Microsoft
description: Wyjaśniono, subskrypcja i wymagania dotyczące zarządzania i przy użyciu usługi Azure AD Privileged Identity Management w dzierżawie licencjonowania
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9e49ddb8fca9ce193c92f27c307942d5f3d3d2fd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Wymagania subskrypcji w usłudze Azure Active Directory Privileged Identity Management

Azure AD Privileged Identity Management jest dostępny w ramach wersji Premium P2 usługi Azure AD. Aby uzyskać więcej informacji o innych funkcjach P2 oraz porównaniu z Premium P1, zobacz [wersje usługi Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Gdy w wersji zapoznawczej usługi Azure Active Directory (Azure AD) Privileged Identity Management, nie było nie są sprawdzane licencji dla dzierżawcy wypróbować usługę.  Teraz, Azure AD Privileged Identity Management została osiągnięta ogólnej dostępności, subskrypcji próbnej lub płatnej musi być obecny dla dzierżawcy kontynuować przy użyciu Privileged Identity Management po grudnia 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Potwierdzenie Twojej wersji próbnej lub płatnej subskrypcji

Jeśli nie masz pewności, czy Twoja organizacja ma okres próbny lub zakupić subskrypcję, można sprawdzić czy za pomocą polecenia uwzględnione w Azure Active Directory modułu dla Windows PowerShell V1 jest subskrypcji w dzierżawie. 
1. Otwórz okno programu PowerShell.
2. Wprowadź `Connect-MsolService` do uwierzytelnienia się jako użytkownik w dzierżawie.
3. Wprowadź `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

To polecenie pobiera listę subskrypcji w dzierżawie. Jeśli nie ma żadnych wierszy, zwracane, należy uzyskać wersji próbnej i komercyjnej Azure AD Premium P2 EMS E5 subskrypcję usługi Azure AD Privileged Identity Management lub subskrypcji usługi Azure AD Premium P2.  Aby uzyskać wersji próbnej i rozpocząć korzystanie z usługi Azure AD Privileged Identity Management, przeczytaj [wprowadzenie do usługi Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Jeśli to polecenie zwraca wiersz, w które SkuPartNumber jest "AAD_PREMIUM_P2" lub "EMSPREMIUM" i IsTrial ma "wartość prawda", oznacza to, że wersji próbnej platformy Azure AD Premium P2 znajduje się w dzierżawie.  Jeśli stan subskrypcji nie jest włączona, a nie masz subskrypcji usługi Azure AD Premium P2 lub EMS E5 zakupu, następnie należy zakupić subskrypcję usługi Azure AD Premium P2 lub subskrypcji EMS E5, aby kontynuować korzystanie z usługi Azure AD Privileged Identity Management.

Jest dostępna za pośrednictwem usługi Azure AD Premium P2 [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [programu licencjonowania zbiorowego Open](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)i [dostawców rozwiązań w chmurze programu](https://partner.microsoft.com/en-US/cloud-solution-provider). Subskrybenci platformy Azure i usługi Office 365 można również kupić Azure AD Premium P2 w trybie online.  Więcej informacji na temat cen usługi Azure AD Premium i sposób sortowania w trybie online można znaleźć w folderze [Azure Active Directory cennik](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Nie jest dostępna w dzierżawie usługi Azure AD Privileged Identity Management

Azure AD Privileged Identity Management nie będą już dostępne w Twojej dzierżawie jeśli:
- Organizacji została przy użyciu usługi Azure AD Privileged Identity Management w wersji zapoznawczej i nie zakupu subskrypcji usługi Azure AD Premium P2 lub EMS E5 subskrypcji.
- Twoja organizacja nie usługi Azure AD Premium P2 lub E5 EMS wersji próbnej, która wygasła.
- Twoja organizacja ma zakupiono subskrypcję, która wygasła.

Po wygaśnięciu subskrypcji Azure AD Premium P2 lub subskrypcji EMS E5 lub jako organizacja, która była za pomocą usługi Azure AD Privileged Identity Management w wersji zapoznawczej nie uzyskał P2 Azure AD Premium lub pakietu EMS E5 subskrypcji:

- Przypisania ról stałych do usługi Azure AD role pozostaną nienaruszone.
- Rozszerzenie usługi Azure AD Privileged Identity Management w portalu Azure, a także interfejsu API programu Graph i polecenia cmdlet programu PowerShell interfejsów Azure AD Privileged Identity Management, nie będą już dostępne dla użytkowników na aktywację ról uprzywilejowanych, zarządzanie uprawnieniami dostępu lub wykonaj dostępu Przegląd ról uprzywilejowanych.
- Przypisania ról kwalifikujących się ról usługi Azure AD zostaną usunięte zgodnie z użytkowników nie będzie już można uaktywniać role uprzywilejowane.
- Zakończy wszystkie przeglądy trwającą dostępu ról usługi Azure AD i będzie można usunąć ustawień konfiguracji usługi Azure AD Privileged Identity Management.
- Azure AD Privileged Identity Management nie będzie wysyłać wiadomości e-mail na zmiany przypisania roli.

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Role w usłudze Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
