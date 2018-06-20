---
title: Co to są strefy dostępności Azure? | Microsoft Docs
description: Aby utworzyć aplikacje wysoką dostępność i odporność na platformie Azure, strefy dostępności zapewniają osobnych lokalizacji, w których można użyć do uruchomienia zasobów.
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 7cfd3fba459f409079cb0c621edab3197070285e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232423"
---
# <a name="what-are-availability-zones-in-azure"></a>Co to są strefy dostępności na platformie Azure?
Dostępność strefy jest oferty, który chroni aplikacje i dane awarii centrum danych wysokiej dostępności. Dostępność strefy są unikatowe lokalizacje fizyczne w obrębie regionu platformy Azure. Każdej strefy składa się z co najmniej jeden centrów danych z niezależnej od zasilania, chłodzenia i sieci. W celu zapewnienia odporności, istnieje co najmniej trzech oddzielnych stref we wszystkich regionach włączone. Fizyczne rozdzielenie dostępności stref w obrębie regionu chroni aplikacje i dane z błędami centrum danych. Usługi strefowo nadmiarowy replikowanie danych i aplikacji w dostępności strefa chroni przed jednym punktów z awarią. W strefach dostępności Azure oferuje branży najlepsze 99,99% wirtualna przestojów umowy SLA. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Strefa dostępności w regionie platformy Azure jest kombinacją domeny błędów i Aktualizacja domeny. Na przykład po utworzeniu co najmniej trzech maszyn wirtualnych różnych strefach trzy w regionie Azure, maszyny wirtualne skutecznie rozproszone na trzy domen błędów i trzy domeny aktualizacji. Platforma Azure rozpoznaje tej dystrybucji między domenami aktualizacji, aby upewnić się, że maszyn wirtualnych w różnych strefach nie są aktualizowane w tym samym czasie.

Tworzenie wysokiej dostępności do architektury aplikacji przez kolokacja zasobów obliczeniowych, magazynu, sieci i danych w ramach strefy i replikacji w innych stref. Usług Azure, które obsługują stref dostępności można podzielić na dwie kategorie:

- **Usługi zonal** — przypiąć zasobów do określonej strefy (na przykład maszyn wirtualnych, dysków zarządzanych adresów IP), lub
- **Usługi strefowo nadmiarowy** — platforma automatycznie replikuje dane między strefy (na przykład magazyn strefowo nadmiarowy, baza danych SQL).

Aby uzyskać kompleksowy ciągłość prowadzenia działalności biznesowej na platformie Azure, kompilacji architektury aplikacji przy użyciu kombinacji stref dostępności z parami region platformy Azure. Można synchronicznie Replikowanie aplikacji i danych przy użyciu stref dostępności w ramach regionu platformy Azure dla wysokiej dostępności i replikowane asynchronicznie w regionach platformy Azure do ochrony odzyskiwania po awarii.
 
![Widok koncepcyjny jedną strefę przechodzi w dół w regionie](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regionach, które obsługują stref dostępności

- Środkowe stany USA
- Francja Środkowa
- Wschodnie stany USA 2 (wersja zapoznawcza)
- Europa Zachodnia (wersja zapoznawcza)
- Południowo-Wschodnia, Azja (wersja zapoznawcza)


## <a name="services-that-support-availability-zones"></a>Usługi obsługujące stref dostępności
Usług Azure, które obsługują dostępność strefy są:

- Maszyny wirtualne z systemem Linux
- Maszyny wirtualne z systemem Windows
- Zestawy skali maszyn wirtualnych
- Managed Disks
- Moduł równoważenia obciążenia
- Publiczny adres IP
- Magazyn strefowo nadmiarowy
- SQL Database


## <a name="pricing"></a>Cennik
Nie ma żadnych dodatkowych kosztów dla maszyn wirtualnych wdrożonych w strefie dostępności. Czas działania maszyny Wirtualnej 99,99% SLA jest oferowany gdy co najmniej dwie maszyny wirtualne są wdrażane w różnych strefach dostępności co najmniej dwa w obrębie regionu platformy Azure. Będzie dodatkowe opłaty transferu danych między dostępności maszyny Wirtualnej VM strefy. Aby uzyskać więcej informacji, przejrzyj [przepustowości ceny](https://azure.microsoft.com/pricing/details/bandwidth/) strony.


## <a name="get-started-with-availability-zones"></a>Wprowadzenie do strefy dostępności
- [Utwórz maszynę wirtualną](../virtual-machines/windows/create-portal-availability-zone.md)
- [Dodaj dysk zarządzane przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Utwórz zestaw skalowania strefy nadmiarowe maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Równoważenie obciążenia maszyn wirtualnych w różnych strefach przy użyciu standardowego modułu równoważenia obciążenia z frontonu strefowo nadmiarowy](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Równoważenie obciążenia maszyn wirtualnych w ramach strefy przy użyciu standardowego modułu równoważenia obciążenia z zonal frontonu](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)


## <a name="next-steps"></a>Kolejne kroki
- [Quickstart templates (Szablony szybkiego startu)](http://aka.ms/azqs)
