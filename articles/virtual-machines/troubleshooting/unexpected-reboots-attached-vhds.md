---
title: Rozwiązywanie nieoczekiwanych ponownych uruchomień maszyn wirtualnych z dołączonymi dyskami VHD na maszynach wirtualnych Azure | Microsoft Docs
description: Jak rozwiązywać problemy z nieoczekiwanym ponownym uruchomieniem maszyn wirtualnych.
keywords: odmówiono połączenia SSH, błąd SSH, usługa Azure SSH, Niepowodzenie połączenia SSH
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086869"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie nieoczekiwanych ponownych uruchomień maszyn wirtualnych z dołączonymi dyskami VHD

Jeśli maszyna wirtualna platformy Azure ma dużą liczbę dołączonych wirtualnych dysków twardych, które znajdują się na tym samym koncie magazynu, może przekroczyć elementy docelowe skalowalności dla poszczególnych kont magazynu, co spowoduje nieoczekiwane ponowne uruchomienie maszyny wirtualnej. Sprawdź metryki minut dla konta magazynu (**TotalRequests**/**TotalIngress**/**TotalEgress**), które przekraczają elementy docelowe skalowalności dla konta magazynu. Zobacz [metryki pokazują wzrost wzrost percentthrottlingerror,](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) Aby uzyskać pomoc w ustaleniu, czy na koncie magazynu nastąpiło ograniczenie przepustowości.

Ogólnie rzecz biorąc każda operacja wejścia lub wyjścia na wirtualnym dysku twardym z maszyny wirtualnej tłumaczy się, aby **pobrać** operacje na stronie lub **umieścić stronę** na bazowym obiekcie blob strony. W związku z tym możesz użyć szacowanej liczby IOPS dla danego środowiska, aby dostroić, ile wirtualnych dysków twardych może znajdować się na jednym koncie magazynu na podstawie określonego zachowania aplikacji. Firma Microsoft zaleca korzystanie z 40 lub mniej dysków na jednym koncie magazynu. Szczegóły dotyczące skalowalności [i wydajności usługi Azure Storage](../../storage/common/storage-scalability-targets.md) można znaleźć w temacie Informacje o celach skalowalności dla kont magazynu, w szczególności o całkowitej liczbie żądań i łącznej przepustowości dla używanego typu konta magazynu.

W przypadku przekroczenia celów skalowalności dla konta magazynu należy umieścić dyski VHD na wielu kontach magazynu, aby zmniejszyć aktywność poszczególnych kont.
