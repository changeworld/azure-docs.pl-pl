---
title: Przenoszenie zasobów sieci wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów | Dokumentacja firmy Microsoft
description: Usługa Azure Resource Manager, aby przenieść sieci wirtualnych do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723495"
---
# <a name="move-guidance-for-virtual-networks"></a>Przenieś wskazówki dotyczące sieci wirtualnych

W tym artykule opisano sposób przenoszenia sieciami wirtualnymi w określonych scenariuszach.

## <a name="dependent-resources"></a>Zasoby zależne

Podczas przenoszenia sieci wirtualnej, możesz również przenieść jego zasobów zależnych. Dla bramy sieci VPN należy przenieść adresy IP, bramy sieci wirtualnej i wszystkie zasoby skojarzone z nimi połączenie. Bramy sieci lokalnej może znajdować się w innej grupie zasobów.

Aby przenieść maszynę wirtualną za pomocą karty interfejsu sieciowego, należy przenieść wszystkie zasoby zależne. Przenieś sieci wirtualnej dla karty interfejsu sieciowego, wszystkie inne karty sieciowe dla sieci wirtualnej i bramy sieci VPN.

## <a name="peered-virtual-network"></a>Wirtualnej sieci równorzędnej

Aby przenieść równorzędnej sieci wirtualnej, należy najpierw wyłączyć komunikacji równorzędnej sieci wirtualnej. Po wyłączeniu można przenieść sieci wirtualnej. Po przeniesieniu ponownie włączyć komunikację równorzędną sieci wirtualnej.

## <a name="subnet-links"></a>Łącza podsieci

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieci za pomocą linków nawigacji zasobów. Na przykład Azure zasobu bazy danych usługi Redis Cache jest wdrożona w podsieci, że podsieć ma linku nawigacji zasobu.

## <a name="next-steps"></a>Następne kroki

Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
