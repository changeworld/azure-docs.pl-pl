---
title: Przenoszenie zasobów sieciowych platformy Azure do nowej subskrypcji lub grupy zasobów | Microsoft Docs
description: Użyj Azure Resource Manager, aby przenieść sieci wirtualne i inne zasoby sieciowe do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626264"
---
# <a name="move-guidance-for-networking-resources"></a>Wskazówki dotyczące przenoszenia zasobów sieciowych

W tym artykule opisano sposób przenoszenia sieci wirtualnych i innych zasobów sieciowych w określonych scenariuszach.

## <a name="dependent-resources"></a>Zasoby zależne

Podczas przenoszenia sieci wirtualnej, możesz również przenieść jego zasobów zależnych. Dla bramy sieci VPN należy przenieść adresy IP, bramy sieci wirtualnej i wszystkie zasoby skojarzone z nimi połączenie. Bramy sieci lokalnej może znajdować się w innej grupie zasobów.

Aby przenieść maszynę wirtualną z kartą sieciową, należy przenieść wszystkie zasoby zależne. Przenieś sieć wirtualną dla karty interfejsu sieciowego, wszystkie inne karty interfejsu sieciowego dla sieci wirtualnej i bramy sieci VPN.

## <a name="state-of-dependent-resources"></a>Stan zasobów zależnych

Jeśli źródłowa lub docelowa Grupa zasobów zawiera sieć wirtualną, Stany wszystkich zasobów zależnych dla sieci wirtualnej są sprawdzane podczas przenoszenia. Jeśli którykolwiek z tych zasobów jest w stanie niepowodzenia, przeniesienie jest zablokowane. Na przykład jeśli maszyna wirtualna, która używa sieci wirtualnej, zakończyła się niepowodzeniem, przenoszenie jest zablokowane. Przenoszenie jest blokowane nawet wtedy, gdy maszyna wirtualna nie jest jednym z przenoszonych zasobów i nie znajduje się w jednej z grup zasobów dla przeniesienia. Aby uniknąć tego problemu, Przenieś zasoby do grupy zasobów, która nie ma sieci wirtualnej.

## <a name="peered-virtual-network"></a>Równorzędna Sieć wirtualna

Aby przenieść równorzędnej sieci wirtualnej, należy najpierw wyłączyć komunikacji równorzędnej sieci wirtualnej. Po wyłączeniu można przenieść sieci wirtualnej. Po przeniesieniu ponownie włączyć komunikację równorzędną sieci wirtualnej.

## <a name="subnet-links"></a>Linki podsieci

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieci za pomocą linków nawigacji zasobów. Na przykład Azure zasobu bazy danych usługi Redis Cache jest wdrożona w podsieci, że podsieć ma linku nawigacji zasobu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
