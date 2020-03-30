---
title: Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023086"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi (PIM) generuje alerty, gdy w organizacji usługi Azure Active Directory (Azure AD) występuje podejrzana lub niebezpieczna aktywność. Po wyzwoleniu alertu pojawia się na stronie Alerty.

![Zasoby platformy Azure — lista alertów, poziomów ryzyka i liczby na stronie Alerty](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Przeglądanie alertów

Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub role, które wyzwoliły alert, wraz ze wskazówkami dotyczącymi korygowania.

![Raport alertów pokazujący czas ostatniego skanowania, opis, kroki ograniczające zagrożenie, typ, ważność, wpływ na bezpieczeństwo i jak zapobiec następnym razem](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alerty

| Alerty | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Zbyt wielu właścicieli przypisanych do zasobu** |Medium |Zbyt wielu użytkowników ma rolę właściciela. |Przejrzyj użytkowników na liście i ponownie przypisz niektórych do ról mniej uprzywilejowanych. |
| **Zbyt wielu stałych właścicieli przypisanych do zasobu** |Medium |Zbyt wielu użytkowników jest trwale przypisanych do roli. |Przejrzyj użytkowników na liście i ponownie przypisz niektórych, aby wymagali aktywacji do wykorzystania ról. |
| **Utworzono zduplikowaną rolę** |Medium |Wiele ról ma te same kryteria. |Użyj tylko jednej z tych ról. |

### <a name="severity"></a>Ważność

- **Wysoki:** Wymaga natychmiastowego działania z powodu naruszenia zasad. 
- **Medium:** Nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
- **Niski:** Nie wymaga natychmiastowych działań, ale sugeruje preferowaną zmianę polityki.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Na stronie Alerty przejdź do **pozycji Ustawienia**.

![Strona Alerty z wyróżnionymi ustawieniami](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Dostosuj ustawienia w różnych alertach, aby pracować z celami środowiska i zabezpieczeń.

![Ustawianie strony alertu w celu włączania i konfigurowania ustawień](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-role-settings.md)
