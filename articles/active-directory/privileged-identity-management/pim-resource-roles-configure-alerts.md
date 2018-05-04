---
title: Zarządzanie alertami zabezpieczeń zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Opisuje alerty zabezpieczeń PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c6c057541b3e3067de6331bab6ca9cccfa092710
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Zarządzanie alertami zabezpieczeń zasobów platformy Azure przy użyciu Privileged Identity Management
Uprzywilejowane Identity Management (PIM) zasobów Azure generuje alerty w razie istnieje podejrzanej lub niebezpieczna działania w danym środowisku. Po wyzwoleniu alertu jest wyświetlane na stronie alerty. 

![Strona alertów](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Alerty dotyczące weryfikacji
Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliła alert, oraz porady korygowania.

![Raport o alertach](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alerty
| Alerty | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Za dużo właścicieli przypisane do zasobu** |Medium |Zbyt wielu użytkowników ma rolę właściciela. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre z mniej uprzywilejowanym rolami. |
| **Zbyt wiele stałych właścicieli przypisane do zasobu** |Medium |Za dużo użytkowników trwale przypisanych do roli. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre do wymagają aktywacji roli. |
| **Zduplikowana roli utworzone** |Medium |Wiele ról ma te same kryteria. |Użyj tylko jednej z tych ról. |


### <a name="severity"></a>Ważność
* **Wysoka**: wymaga natychmiastowego działania z powodu naruszenia zasad. 
* **Średnia liczba godzin**: nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niski**: nie wymagać natychmiastowego działania, ale sugeruje zmianę zasady preferowany.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Na stronie alerty, przejdź do **ustawienia**.
![Ustawienia](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Dostosowywanie ustawień na różnych alerty do pracy z własnego środowiska i cele zabezpieczeń.
![Dostosowywanie ustawień](media/azure-pim-resource-rbac/rbac-alert-settings.png)
