---
title: Przenoszenie klasycznych zasobów wdrażania platformy Azure
description: Użyj Azure Resource Manager, aby przenieść klasyczne zasoby wdrożenia do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150843"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Wskazówki dotyczące przenoszenia dla zasobów klasycznego modelu wdrażania

Kroki dotyczące przenoszenia zasobów wdrożonych za pośrednictwem modelu klasycznego różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji, czy na nową subskrypcję.

## <a name="move-in-the-same-subscription"></a>Przenieś w tej samej subskrypcji

Podczas przenoszenia zasobów między grupami zasobów do innej grupy zasobów w ramach tej samej subskrypcji, obowiązują następujące ograniczenia:

* Nie można przenieść sieci wirtualne (klasyczne).
* Maszyny wirtualne (klasyczne) należy przenieść z usługą w chmurze.
* Usługa w chmurze mogą być przenoszone po przeniesieniu obejmuje wszystkie maszyny wirtualne.
* Jednocześnie można przenosić tylko jedną usługę w chmurze.
* Jednocześnie można przenosić tylko jedno konto magazynu (klasyczne).
* Nie można przenieść konta magazynu (klasyczne) w tej samej operacji przy użyciu maszyny wirtualnej lub usługi w chmurze.

Aby przenieść zasoby klasyczne do nowej grupy zasobów w ramach tej samej subskrypcji, użyj [standardowych operacji przenoszenia](../resource-group-move-resources.md) w portalu, Azure PowerShell, INTERFEJSIE wiersza polecenia platformy Azure lub interfejsu API REST. Możesz użyć tych samych operacji jak używane do przenoszenia zasobów usługi Resource Manager.

## <a name="move-across-subscriptions"></a>Przechodzenie między subskrypcjami

Podczas przenoszenia zasobów do nowej subskrypcji, obowiązują następujące ograniczenia:

* Wszystkie zasoby klasyczne w subskrypcji należy przenieść w tej samej operacji.
* Subskrypcja docelowa nie może mieć żadnych innych zasobów klasycznych.
* Przeniesienie można jedynie żądać użycie oddzielnych interfejsu API REST, aby poruszać się klasycznego. Standardowe polecenia move Menedżera zasobów nie działają podczas przenoszenia zasobów klasycznych do nowej subskrypcji.

Aby przenieść zasobów klasycznych do nowej subskrypcji, użyj operacji REST, które są specyficzne dla zasobów klasycznych. Aby użyć REST, wykonaj następujące czynności:

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

Jeśli masz problem z przeniesieniem zasobów klasycznych, skontaktuj się z [pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
