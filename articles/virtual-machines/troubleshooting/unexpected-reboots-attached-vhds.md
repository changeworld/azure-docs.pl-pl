---
title: Rozwiązywanie problemów z nieoczekiwane ponowne uruchamianie maszyn wirtualnych z dołączonymi dyskami VHD na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z nieoczekiwane ponowne uruchamianie maszyn wirtualnych.
keywords: SSH połączenia zostało odrzucone, ssh błąd, azure ssh, połączenie SSH nie powiodło się
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 6273087e28be8b784168d5808918d04d0e4cf303
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443725"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwane ponowne uruchamianie maszyn wirtualnych z dołączonymi dyskami VHD

Jeśli maszyna wirtualna platformy Azure (VM) ma dużą liczbę dołączonymi dyskami VHD, które znajdują się w tym samym koncie magazynu, może przekraczać wartości docelowe skalowalności konta magazynu, powodując nieoczekiwane ponowne uruchomienie maszyny Wirtualnej. Sprawdź metryki minut dla konta magazynu (**TotalRequests**/**TotalIngress**/**TotalEgress**) dla wartości graniczne, które przekraczają wartości docelowe skalowalności konta magazynu. Zobacz [metryki spowodować wzrost wartości PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) uzyskać pomoc w określaniu, czy ograniczenie wystąpił na swoim koncie magazynu.

Ogólnie rzecz biorąc każdy poszczególnych danych wejściowych lub danych wyjściowych operacji na wirtualny dysk twardy z maszyny wirtualnej przekłada się na **pobrać strony** lub **umieścić strony** operacji na podstawowej stronicowych obiektów blob. W związku z tym można użyć szacowany operacje We/Wy dla danego środowiska do dostrojenia jak wiele wirtualnych dysków twardych, może mieć w ramach pojedynczego konta magazynu na podstawie określonego zachowania aplikacji. Firma Microsoft zaleca posiadanie 40 lub mniejszą liczbę dysków w ramach pojedynczego konta magazynu. Zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../../storage/common/storage-scalability-targets.md) szczegółowe informacje na temat wartości docelowe skalowalności konta magazynu, w szczególności łączna liczba żądań zakończonych i całkowitej przepustowości dla typu konta magazynu używasz.

Jeśli doszło do przekroczenia cele skalowalności konta magazynu, należy umieścić Twoje dyski VHD w ramach wielu kont magazynu, aby zmniejszyć działania w ramach każdego konta poszczególnych.
