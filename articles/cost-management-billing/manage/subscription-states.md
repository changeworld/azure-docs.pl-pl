---
title: Stany subskrypcji platformy Azure
description: Opis różnych stanów subskrypcji platformy Azure
keywords: stan subskrypcji platformy azure
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200541"
---
# <a name="azure-subscription-states"></a>Stany subskrypcji platformy Azure
W tym artykule opisano różne możliwe stany subskrypcji platformy Azure. Te stany są wyświetlane jako „Stan” w blokach subskrypcji.

| Stan subskrypcji | Opis |
|-------------| ----------------|
| **Aktywna** | Twoja subskrypcja platformy Azure jest aktywna. Możesz używać tej subskrypcji do wdrażania nowych zasobów i zarządzania istniejącymi.|
| **Minął termin płatności** | Istnieje zaległa płatność za Twoją subskrypcję platformy Azure. Subskrypcja jest nadal aktywna, ale nieopłacenie zaległych płatności może spowodować wyłączenie subskrypcji. [Rozwiąż problem z zaległym saldem za subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance). |
| **Disabled (Wyłączone)** | Twoja subskrypcja platformy Azure jest wyłączona i nie można już używać jej do tworzenia zasobów platformy Azure i zarządzania nimi. W tym stanie przydziały maszyn wirtualnych są wycofane, tymczasowe adresy IP są zwolnione, magazyn jest dostępny tylko do odczytu, a inne usługi są wyłączone. Subskrypcja może zostać wyłączona, ponieważ Twoje środki wygasły, osiągnięto limit wydatków, masz zaległy rachunek, przekroczono limit karty kredytowej lub subskrypcja została jawnie wyłączona/anulowana. Zależnie od typu subskrypcji i przyczyny wyłączenia, subskrypcja może pozostać wyłączona przez 1–90 dni, po czym zostanie trwale usunięta. [Ponownie uaktywnij wyłączoną subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).|
| **Usunięta** | Twoja subskrypcja platformy Azure została usunięta wraz z wszystkimi zasobami/danymi bazowymi. |
