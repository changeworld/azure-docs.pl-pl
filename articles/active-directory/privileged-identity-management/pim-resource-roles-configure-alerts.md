---
title: Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476330"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM
Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generuje alerty w przypadku podejrzanej lub niebezpieczne działania w środowisku. Po wyzwoleniu alertu ona wyświetlona na stronie alertów. 

![Zasoby platformy Azure — stronie alertów, wyświetlanie listy alertów, poziom ryzyka i liczba](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Przeglądanie alertów
Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwolony alert, wraz z porad korygowania.

![Raport o alertach przedstawiający ostatniego skanowania czasu, opis, kroki zaradcze, typ, ważność, wpływ na bezpieczeństwo i jak temu zapobiec w przyszłości](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alerty
| Alerty | severity | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Zbyt wielu właścicieli przypisanych do zasobu** |Średni |Zbyt wielu użytkowników korzysta z roli właściciela. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre mniej uprzywilejowanych ról. |
| **Zbyt wiele trwałych właścicieli przypisanych do zasobu** |Średni |Zbyt wielu użytkowników są trwale przypisane do roli. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre do wymagana aktywacja w celu użycia roli. |
| **Utworzono zduplikowaną rolę** |Średni |Wiele ról ma takich samych kryteriów. |Użyj tylko jednej z tych ról. |


### <a name="severity"></a>severity
* **Wysoka**: Wymaga natychmiastowego działania z powodu naruszenia zasad. 
* **Średnia**: Nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: Nie wymagać natychmiastowego działania, ale sugeruje zmiana preferowanego zasad.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Na stronie alertów, przejdź do **ustawienia**.

![Strona alertów przy użyciu ustawień z wyróżnioną pozycją](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Dostosowywanie ustawień na różne alerty chcesz pracować w swoim środowisku i cele zabezpieczeń.

![Ustawianie strony alertu włączyć i skonfigurować ustawienia](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
