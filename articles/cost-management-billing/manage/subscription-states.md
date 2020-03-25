---
title: Stany subskrypcji platformy Azure
description: W tym artykuł opisano różne stany subskrypcji platformy Azure.
keywords: stan subskrypcji platformy azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78270240"
---
# <a name="azure-subscription-states"></a>Stany subskrypcji platformy Azure

W tym artykule opisano różne możliwe stany subskrypcji platformy Azure. Te stany są wyświetlane jako **Stan** w obszarach subskrypcji witryny Azure Portal.

| Stan subskrypcji | Opis |
|-------------| ----------------|
| **Aktywna** | Twoja subskrypcja platformy Azure jest aktywna. Możesz używać subskrypcji do wdrażania nowych zasobów i zarządzania istniejącymi.|
| **Usunięta** | Twoja subskrypcja platformy Azure została usunięta wraz z wszystkimi zasobami/danymi bazowymi. |
| **Disabled (Wyłączone)** | Twoja subskrypcja platformy Azure jest wyłączona i nie można już używać jej do tworzenia zasobów platformy Azure i zarządzania nimi. W tym stanie przydziały maszyn wirtualnych są wycofane, tymczasowe adresy IP są zwolnione, magazyn jest dostępny tylko do odczytu, a inne usługi są wyłączone. Subskrypcja może zostać wyłączona z następujących powodów: Twoje środki mogły wygasnąć. Prawdopodobnie osiągnięto limit wydatków. Masz zaległy rachunek. Przekroczono limit karty kredytowej. Subskrypcja została jawnie wyłączona lub anulowana. W zależności od typu subskrypcji subskrypcja może pozostać wyłączona od 1 do 90 dni. Po upływie tego czasu zostanie ona trwale usunięta. Aby uzyskać więcej informacji, zobacz [Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure](subscription-disabled.md). |
| **Wygasła** | Twoja subskrypcja platformy Azure wygasła, ponieważ została anulowana. Możesz ponownie aktywować wygasłą subskrypcję. Aby uzyskać więcej informacji, zobacz [Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure](subscription-disabled.md).|
| **Minął termin płatności** | Istnieje zaległa płatność za Twoją subskrypcję platformy Azure. Subskrypcja jest nadal aktywna, ale nieopłacenie zaległych płatności może spowodować wyłączenie subskrypcji. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure](resolve-past-due-balance.md). |
