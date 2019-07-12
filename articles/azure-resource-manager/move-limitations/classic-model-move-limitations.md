---
title: Przenieś zasoby dotyczące wdrażania klasycznej platformy Azure do nowej subskrypcji lub grupy zasobów
description: Usługa Azure Resource Manager, aby przenieść zasoby dotyczące wdrażania klasycznego do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723508"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Przenieś wskazówki dotyczące zasobów modelu wdrożenia klasycznego

Kroki wymagane do przeniesienia zasobów wdrożonych za pośrednictwem modelu klasycznego, różnią się zależnie od tego, czy przenosisz zasoby w ramach subskrypcji lub do nowej subskrypcji.

## <a name="move-in-the-same-subscription"></a>Przenieś do tej samej subskrypcji

Podczas przenoszenia zasobów między grupami zasobów do innej grupy zasobów w ramach tej samej subskrypcji, obowiązują następujące ograniczenia:

* Nie można przenieść sieci wirtualne (klasyczne).
* Maszyny wirtualne (klasyczne) należy przenieść z usługą w chmurze.
* Usługa w chmurze mogą być przenoszone po przeniesieniu obejmuje wszystkie maszyny wirtualne.
* Jednocześnie można przenosić tylko jedną usługę w chmurze.
* Jednocześnie można przenosić tylko jedno konto magazynu (klasyczne).
* Nie można przenieść konta magazynu (klasyczne) w tej samej operacji przy użyciu maszyny wirtualnej lub usługi w chmurze.

Można przenieść zasobów klasycznych do nowej grupy zasobów w ramach tej samej subskrypcji, za pomocą [operacji przenoszenia standardowa](../resource-group-move-resources.md) za pośrednictwem portalu, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Możesz użyć tych samych operacji jak używane do przenoszenia zasobów usługi Resource Manager.

## <a name="move-across-subscriptions"></a>Przenoszenie między subskrypcjami

Podczas przenoszenia zasobów do nowej subskrypcji, obowiązują następujące ograniczenia:

* Wszystkie zasoby klasyczne w subskrypcji należy przenieść w tej samej operacji.
* Subskrypcja docelowa nie może mieć inne zasoby klasyczne.
* Przeniesienie można jedynie żądać użycie oddzielnych interfejsu API REST, aby poruszać się klasycznego. Standardowe polecenia move Menedżera zasobów nie działają podczas przenoszenia zasobów klasycznych do nowej subskrypcji.

Aby przenieść zasobów klasycznych do nowej subskrypcji, użyj operacji REST, które są specyficzne dla zasobów klasycznych. Aby korzystanie z interfejsu REST, wykonaj następujące czynności:

1. Sprawdź, jeśli subskrypcja źródłowa mogą brać udział w przenoszenia między subskrypcjami. Użyj następujących operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     W treści żądania obejmują:

   ```json
   {
    "role": "source"
   }
   ```

     Odpowiedź dla operacji sprawdzania poprawności znajduje się w następującym formacie:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Sprawdź, jeśli subskrypcja docelowa mogą brać udział w przenoszenia między subskrypcjami. Użyj następujących operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     W treści żądania obejmują:

   ```json
   {
    "role": "target"
   }
   ```

     Odpowiedź jest w formacie sprawdzania poprawności subskrypcji źródłowej.
1. Jeśli obie subskrypcje pomyślnie weryfikacji, Przenieś wszystkie zasoby klasyczne z jednej subskrypcji do innej subskrypcji za pomocą następujących operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    W treści żądania obejmują:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Operacja może trwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z przenoszeniem zasobów klasycznych, skontaktuj się z pomocą [pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
