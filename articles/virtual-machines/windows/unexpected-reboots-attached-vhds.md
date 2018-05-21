---
title: Rozwiązywanie problemów z nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD na maszynach wirtualnych Windows Azure | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z nieoczekiwane ponowne uruchomienie maszyn wirtualnych systemu Windows.
keywords: SSH połączenia zostało odrzucone, ssh błędu, platforma azure ssh, połączenia SSH nie powiodło się
services: virtual-machines-windows
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 5c16c2d08a1a317f8f718330ff6c86a452ac1815
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD

Jeśli maszyna wirtualna Azure (VM) jest duża liczba dołączonych dysków VHD, które znajdują się w tym samym koncie magazynu, może przekroczyć wartości docelowe skalowalności konta magazynu powoduje nieoczekiwane ponowne uruchomienie maszyny Wirtualnej. Sprawdź minuty metryki dla konta magazynu (**TotalRequests**/**TotalIngress**/**TotalEgress**) maksymalnej, które przekraczają wartości docelowe skalowalności konta magazynu. Zobacz [metryki spowodować wzrost PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) Aby uzyskać pomoc w określeniu, czy ograniczania wystąpił na koncie magazynu.

Ogólnie rzecz biorąc, każdej poszczególnych danych wejściowych lub wyjściowych operacji na wirtualny dysk twardy z maszyny wirtualnej przekłada się **Get strony** lub **umieścić strony** operacje na podstawowym stronicowych obiektów blob. W związku z tym umożliwia szacowany IOPS dla danego środowiska Dostosuj liczbę dysków VHD, użytkownik może mieć na koncie magazynu jednego na podstawie określone zachowanie aplikacji. Firma Microsoft zaleca o 40 lub mniejszą liczbę dysków na koncie magazynu jednego. Zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../../storage/common/storage-scalability-targets.md) szczegółowe informacje o wartości docelowe skalowalności w przypadku kont magazynu, w szczególności liczby całkowitej żądań i łączną przepustowość dla typu konta magazynu są używane.

Jeśli doszło do przekroczenia wartości docelowe skalowalności konta magazynu, należy umieścić dyski VHD w wielu kont magazynu w celu ograniczenia działania w poszczególnych poszczególnych kont.
