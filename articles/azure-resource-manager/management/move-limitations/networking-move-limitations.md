---
title: Przenoszenie zasobów usługi Azure Networking do nowej subskrypcji lub grupy zasobów
description: Usługa Azure Resource Manager umożliwia przenoszenie sieci wirtualnych i innych zasobów sieciowych do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485236"
---
# <a name="move-guidance-for-networking-resources"></a>Przenoszenie wskazówek dotyczących zasobów sieciowych

W tym artykule opisano sposób przenoszenia sieci wirtualnych i innych zasobów sieciowych dla określonych scenariuszy.

## <a name="dependent-resources"></a>Zasoby zależne

Podczas przenoszenia sieci wirtualnej należy również przenieść jej zasoby zależne. W przypadku bram sieci VPN należy przenieść adresy IP, bramy sieci wirtualnej i wszystkie skojarzone zasoby połączeń. Bramy sieci lokalnej mogą znajdować się w innej grupie zasobów.

Aby przenieść maszynę wirtualną z kartą interfejsu sieciowego do nowej subskrypcji, należy przenieść wszystkie zasoby zależne. Przenieś sieć wirtualną dla karty interfejsu sieciowego, wszystkich innych kart interfejsu sieciowego dla sieci wirtualnej i bram sieci VPN.

Aby uzyskać więcej informacji, zobacz [Scenariusz przechodzenia między subskrypcjami](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Sieć wirtualna komunikacji równorzędnej

Aby przenieść sieć wirtualną równorzędną, należy najpierw wyłączyć komunikację równorzędną sieci wirtualnej. Po wyłączeniu można przenieść sieć wirtualną. Po przeprowadzce ponownie można ponownie wywrzeć komunikacji równorzędnej sieci wirtualnej.

## <a name="subnet-links"></a>Łącza podsieci

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieć z łączami nawigacyjnymi zasobów. Na przykład jeśli zasób Usługi Azure Cache for Redis jest wdrażany w podsieci, ta podsieć ma łącze nawigacji zasobów.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
