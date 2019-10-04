---
title: Stany subskrypcji platformy Azure
description: Opis różnych stanów subskrypcji platformy Azure
keywords: stan subskrypcji platformy azure
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 62c21c988f69c14b5d62c618163b172e2fa10397
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71341083"
---
# <a name="azure-subscription-states"></a>Stany subskrypcji platformy Azure
W tym artykule opisano różne możliwe stany subskrypcji platformy Azure. Te stany są wyświetlane jako „Stan” w blokach subskrypcji.

| Stan subskrypcji | Opis |
|-------------| ----------------|
| **Aktywna** | Twoja subskrypcja platformy Azure jest aktywna. Możesz używać tej subskrypcji do wdrażania nowych zasobów i zarządzania istniejącymi.|
| **Minął termin płatności** | Istnieje zaległa płatność za Twoją subskrypcję platformy Azure. Subskrypcja jest nadal aktywna, ale nieopłacenie zaległych płatności może spowodować wyłączenie subskrypcji. [Rozwiąż problem z zaległym saldem za subskrypcję platformy Azure](https://docs.microsoft.com/en-us/azure/billing/billing-azure-subscription-past-due-balance). |
| **Wyłączona** | Twoja subskrypcja platformy Azure jest wyłączona i nie można już używać jej do tworzenia zasobów platformy Azure i zarządzania nimi. W tym stanie przydziały maszyn wirtualnych są wycofane, tymczasowe adresy IP są zwolnione, magazyn jest dostępny tylko do odczytu, a inne usługi są wyłączone. Subskrypcja może zostać wyłączona, ponieważ Twoje środki wygasły, osiągnięto limit wydatków, masz zaległy rachunek, przekroczono limit karty kredytowej lub subskrypcja została jawnie wyłączona/anulowana. Zależnie od typu subskrypcji i przyczyny wyłączenia, subskrypcja może pozostać wyłączona przez 1–90 dni, po czym zostanie trwale usunięta. [Ponownie uaktywnij wyłączoną subskrypcję platformy Azure](https://docs.microsoft.com/en-us/azure/billing/billing-subscription-become-disable).|
| **Usunięta** | Twoja subskrypcja platformy Azure została usunięta wraz z wszystkimi zasobami/danymi bazowymi. |
