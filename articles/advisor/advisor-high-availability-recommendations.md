---
title: Zalecenia usługi Advisor wysokiej dostępności Azure | Dokumentacja firmy Microsoft
description: Za pomocą usługi Azure doradcy zwiększenia wysokiej dostępności Azure wdrożeń.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 23764b476f01c30b1755c507a0cfa5ead27be91e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736555"
---
# <a name="advisor-high-availability-recommendations"></a>Zalecenia doradcy w zakresie wysokiej dostępności

Azure Advisor pomaga zapewnić i zapewnić ciągłość aplikacji biznesowych o znaczeniu krytycznym. Można uzyskać wysoką dostępność zalecenia usługi Advisor z **wysokiej dostępności** pulpitu nawigacyjnego usługi Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zapewnić odporność na uszkodzenia maszyny wirtualnej

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Klasyfikator identyfikuje maszyn wirtualnych, które nie są częścią zestawu dostępności i zaleca przenoszenia ich do zestawu dostępności. Ta konfiguracja temu podczas albo planowanego lub nieplanowanego zdarzenia konserwacji, co najmniej jednej maszyny wirtualnej jest dostępna i spełnia umowy SLA dla maszyny wirtualnej platformy Azure. Można wybrać, aby utworzyć zbiór dostępności dla maszyny wirtualnej lub Dodaj maszynę wirtualną do istniejącego zestawu dostępności.

> [!NOTE]
> Jeśli wybierzesz opcję utworzenia zestawu dostępności, należy dodać do niego co najmniej jednej maszyny wirtualnej więcej. Zaleca się tego grupowanie co najmniej dwie maszyny wirtualne w dostępności ustawioną upewnij się, że co najmniej jedna maszyna jest dostępna podczas wystąpienia awarii.

## <a name="ensure-availability-set-fault-tolerance"></a>Upewnij się, że odporność na uszkodzenia zestawu dostępności 

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Klasyfikator identyfikuje zestawów dostępności, które zawierają jednej maszyny wirtualnej i zaleca dodanie do niej co najmniej jednej maszyny wirtualnej. Ta konfiguracja temu podczas albo planowanego lub nieplanowanego zdarzenia konserwacji, co najmniej jednej maszyny wirtualnej jest dostępna i spełnia umowy SLA dla maszyny wirtualnej platformy Azure. Można wybrać do utworzenia maszyny wirtualnej lub aby dodać istniejącą maszynę wirtualną do zestawu dostępności.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Zapewnić odporność na uszkodzenia bramy aplikacji
Aby zapewnić ciągłość kluczowych aplikacji, które są obsługiwane przez usługę bramy aplikacji, usługi Advisor identyfikuje wystąpień bramy aplikacji, które nie są skonfigurowane na uszkodzenia, i sugeruje akcji korygowania, które należy wykonać. Średnich i dużych pojedyncze wystąpienie aplikacji bramy identyfikuje Advisor i zaleca się dodawania co najmniej jedno wystąpienie więcej. Również instance jednego lub kilku aplikacji małych bramy identyfikuje i zaleca migracji do średnich i dużych jednostki SKU. Klasyfikator zaleca te akcje, aby upewnić się, że z wystąpień bramy aplikacji są skonfigurowane do spełnia bieżące wymagania umowy SLA dla tych zasobów.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Zwiększyć wydajność i niezawodność dysków maszyny wirtualnej

Klasyfikator identyfikuje maszyn wirtualnych przy użyciu standardowych dysków i zaleca się uaktualniania do dysków premium.
 
Usługa Azure Premium Storage oferuje obsługę dysków o wysokiej wydajności i małych opóźnieniach maszyn wirtualnych uruchomionych obciążeń/O wykonujących. Dyski maszyn wirtualnych, które używają konta premium magazynu przechowywania danych na dyskach półprzewodnikowych (SSD). Aby uzyskać najlepszą wydajność aplikacji zaleca się przeprowadzenie migracji wszystkich dysków maszyny wirtualnej wymagające wysokiej wartość IOPS dla magazyn w warstwie premium. 

Jeśli dyski nie wymagają wysokiej IOPS, można ograniczyć koszty, przechowując ich magazynu w warstwie standardowa. Standardowe magazynu przechowuje dane dysku maszyny wirtualnej na stacje dysków twardych (HDD) zamiast dysków SSD. Można przeprowadzić migrację dysków maszyny wirtualnej do dysków premium. Dyski w warstwie Premium są obsługiwane w większości maszyny wirtualnej jednostki SKU. Jednak w niektórych przypadkach, jeśli chcesz używać dysków premium, może być konieczne uaktualnienie maszyny wirtualnej jednostki SKU również.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrona danych maszyny wirtualnej przed przypadkowym usunięciem
Konfigurowanie kopii zapasowej maszyny wirtualnej zapewnia dostępność danych biznesowych o znaczeniu krytycznym i zapewnia ochronę przed przypadkowym usunięciem lub uszkodzenia.  Klasyfikator identyfikuje maszyny wirtualne, których kopia zapasowa nie jest włączona i zaleca się włączenie kopii zapasowej. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów chmury Azure, w razie konieczności
Podczas uruchamiania obciążeń biznesowych o znaczeniu krytycznym, należy mieć dostęp do pomocy technicznej, gdy jest wymagane. Klasyfikator identyfikuje potencjalne subskrypcje krytycznym znaczeniu, które nie mają pomocy technicznej zawarte w ich plan pomocy technicznej i zaleca się uaktualniania do opcji z obsługą techniczną.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak uzyskać dostęp wysokiej dostępności zalecenia usługi Advisor

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor, kliknij przycisk **wysokiej dostępności** kartę.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących zalecenia doradcy w zakresie zobacz:
* [Wprowadzenie do usługi Advisor Azure](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia doradcy w zakresie koszt](advisor-performance-recommendations.md)
* [Zalecenia doradcy w zakresie wydajności](advisor-performance-recommendations.md)
* [Zalecenia doradcy w zakresie zabezpieczeń](advisor-security-recommendations.md)

