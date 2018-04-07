---
title: Privileged Identity Management zasobów Azure - alerty zabezpieczeń | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - role zasobów — alerty
PIM zasobów Azure generuje alerty w przypadku podejrzanej lub niebezpieczna działania w danym środowisku. Po wyzwoleniu alertu jest wyświetlane na stronie alerty. 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Alerty dotyczące weryfikacji
Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które spowodowało oraz porady korygowania.
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alerty
| Alerty | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Za dużo właścicieli przypisane do zasobu** |Medium |Zbyt wielu użytkowników ma rolę właściciela. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre z mniej uprzywilejowanym rolami. |
| **Zbyt wiele stałych właścicieli przypisane do zasobu** |Medium |Za dużo użytkowników trwale przypisanych do roli. |Przejrzyj użytkowników na liście, a następnie ponownie przypisać niektóre do wymagają aktywacji roli. |
| **Zduplikowana roli utworzone** |Medium |Wiele ról ma te same kryteria. |Tylko użyj jednej z tych ról. |


### <a name="severity"></a>Ważność
* **Wysoka**: wymaga natychmiastowego działania z powodu naruszenia zasad. 
* **Średnia liczba godzin**: nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niski**: nie wymagać natychmiastowego działania, ale sugeruje zmianę preferrable zasady.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Na stronie Alerty przejdź do ustawień.
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Dostosowywanie ustawień na różnych alerty do pracy z własnego środowiska i cele zabezpieczeń.
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
