---
title: Przenoszenie zasobów wdrażania usługi Azure Classic
description: Użyj usługi Azure Resource Manager, aby przenieść zasoby wdrożenia klasycznego do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485288"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Przenoszenie wskazówek dotyczących zasobów modelu wdrażania klasycznego

Kroki, aby przenieść zasoby wdrożone za pośrednictwem modelu klasycznego różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji lub do nowej subskrypcji.

## <a name="move-in-the-same-subscription"></a>Przechodzenie w tej samej subskrypcji

Podczas przenoszenia zasobów z jednej grupy zasobów do innej grupy zasobów w ramach tej samej subskrypcji obowiązują następujące ograniczenia:

* Sieci wirtualnych (klasycznych) nie można przenosić.
* Maszyny wirtualne (klasyczne) muszą być przenoszone z usługą w chmurze.
* Usługa w chmurze można przenieść tylko wtedy, gdy przeniesienie obejmuje wszystkie jego maszyny wirtualne.
* Jednocześnie można przenosić tylko jedną usługę w chmurze.
* Jednocześnie można przenosić tylko jedno konto magazynu (klasyczne).
* Konta magazynu (klasycznego) nie można przenieść w tej samej operacji za pomocą maszyny wirtualnej lub usługi w chmurze.

Aby przenieść zasoby klasyczne do nowej grupy zasobów w ramach tej samej subskrypcji, należy użyć [standardowych operacji przenoszenia](../move-resource-group-and-subscription.md) za pośrednictwem portalu, usługi Azure PowerShell, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Te same operacje są używane do przenoszenia zasobów Menedżera zasobów.

## <a name="move-across-subscriptions"></a>Przechodzenie między subskrypcjami

Podczas przenoszenia zasobów do nowej subskrypcji obowiązują następujące ograniczenia:

* Wszystkie zasoby klasyczne w subskrypcji muszą zostać przeniesione w tej samej operacji.
* Subskrypcja docelowa nie może mieć żadnych innych zasobów klasycznych.
* Przeniesienie można zażądać tylko za pośrednictwem oddzielnego interfejsu API REST dla klasycznych ruchów. Standardowe polecenia przenoszenia Menedżera zasobów nie działają podczas przenoszenia zasobów klasycznych do nowej subskrypcji.

Aby przenieść zasoby klasyczne do nowej subskrypcji, należy użyć operacji REST, które są specyficzne dla zasobów klasycznych. Aby użyć restu, wykonaj następujące czynności:

1. Sprawdź, czy subskrypcja źródłona może uczestniczyć w przenoszeniu subskrypcji krzyżowej. Wykonać następującą operację:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     W treści żądania obejmują:

   ```json
   {
    "role": "source"
   }
   ```

     Odpowiedź dla operacji sprawdzania poprawności jest w następującym formacie:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Sprawdź, czy subskrypcja docelowa może uczestniczyć w przenoszeniu subskrypcji krzyżowej. Wykonać następującą operację:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     W treści żądania obejmują:

   ```json
   {
    "role": "target"
   }
   ```

     Odpowiedź jest w tym samym formacie co sprawdzanie poprawności subskrypcji źródłowej.
1. Jeśli obie subskrypcje przechodzą sprawdzanie poprawności, przenieś wszystkie klasyczne zasoby z jednej subskrypcji do innej subskrypcji za pomocą następującej operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    W treści żądania obejmują:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Operacja może działać przez kilka minut.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z przenoszeniem klasycznych zasobów, skontaktuj się z [pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Aby uzyskać polecenia przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
