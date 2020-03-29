---
title: Rozwiązywanie problemów z nieoczekiwanym ponownym uruchomieniem maszyn wirtualnych za pomocą dołączonych identyfikatorów VHD na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Jak rozwiązać problem nieoczekiwanych ponownych rozruchów maszyn wirtualnych.
keywords: połączenie ssh odmówił, błąd ssh, azure ssh, połączenie SSH nie powiodło się
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75358530"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwanym ponownym uruchomieniem maszyn wirtualnych za pomocą dołączonych wirtualnych

Jeśli maszyna wirtualna platformy Azure (VM) ma dużą liczbę dołączonych identyfikatorów wirtualnych, które znajdują się na tym samym koncie magazynu, można przekroczyć cele skalowalności dla konta magazynu indywidualnego, powodując nieoczekiwane ponowne uruchomienie maszyny Wirtualnej. Sprawdź metryki minut dla konta magazynu **(TotalRequests**/**TotalIngress**/**TotalEgress)** dla skoków, które przekraczają cele skalowalności dla konta magazynu. Zobacz [metryki pokaż wzrost PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) pomocy w określaniu, czy dławienie wystąpiło na koncie magazynu.

Ogólnie rzecz biorąc każda pojedyncza operacja wejścia lub wyjścia na dysku VHD z maszyny wirtualnej tłumaczy **się na Pobierz stronę** lub Umieść operacje **strony** na podstawowym stronicowym obiekcie blob. W związku z tym można użyć szacowane we/wy dla danego środowiska, aby dostroić liczbę dysków wirtualnych, które można mieć na jednym koncie magazynu na podstawie określonego zachowania aplikacji. Firma Microsoft zaleca posiadanie 40 lub mniej dysków na jednym koncie magazynu. Aby uzyskać więcej informacji na temat celów skalowalności dla standardowych kont magazynu, zobacz [Cele skalowalności dla standardowych kont magazynu](../../storage/common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji na temat celów skalowalności dla kont magazynu obiektów blob strony premium, zobacz [Cele skalowalności dla kont magazynu obiektów blob strony premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Jeśli przekraczasz cele skalowalności dla konta magazynu, umieść dyski VHD na wielu kontach magazynu, aby zmniejszyć aktywność na każdym koncie.
