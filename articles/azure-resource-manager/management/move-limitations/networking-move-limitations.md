---
title: Przenoszenie zasobów sieciowych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść sieci wirtualne i inne zasoby sieciowe do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485236"
---
# <a name="move-guidance-for-networking-resources"></a>Wskazówki dotyczące przenoszenia zasobów sieciowych

W tym artykule opisano sposób przenoszenia sieci wirtualnych i innych zasobów sieciowych w określonych scenariuszach.

## <a name="dependent-resources"></a>Zasoby zależne

Podczas przenoszenia sieci wirtualnej, możesz również przenieść jego zasobów zależnych. Dla bramy sieci VPN należy przenieść adresy IP, bramy sieci wirtualnej i wszystkie zasoby skojarzone z nimi połączenie. Bramy sieci lokalnej może znajdować się w innej grupie zasobów.

Aby przenieść maszynę wirtualną z kartą sieciową do nowej subskrypcji, musisz przenieść wszystkie zasoby zależne. Przenieś sieć wirtualną dla karty interfejsu sieciowego, wszystkie inne karty interfejsu sieciowego dla sieci wirtualnej i bramy sieci VPN.

Aby uzyskać więcej informacji, zobacz [scenariusz przenoszenia między subskrypcjami](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Równorzędna Sieć wirtualna

Aby przenieść równorzędnej sieci wirtualnej, należy najpierw wyłączyć komunikacji równorzędnej sieci wirtualnej. Po wyłączeniu można przenieść sieci wirtualnej. Po przeniesieniu ponownie włączyć komunikację równorzędną sieci wirtualnej.

## <a name="subnet-links"></a>Linki podsieci

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieci za pomocą linków nawigacji zasobów. Na przykład Azure zasobu bazy danych usługi Redis Cache jest wdrożona w podsieci, że podsieć ma linku nawigacji zasobu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
