---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a7a4e4b487c324bada818d4815f253110f7f7a60
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235086"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Standardowy dysk SSD Managed Disks dla obciążeń maszyn wirtualnych platformy Azure

Usługa Azure pełny stan dyski (SSD w warstwie standardowa) Managed Disks to opcja ekonomicznego magazynowania, zoptymalizowanych pod kątem obciążeń wymagających spójną wydajność na niższych poziomach operacji We/Wy. SSD w warstwie standardowa oferuje środowisko dobre klasy podstawowej dla osób, które chcesz przenieść do chmury, zwłaszcza, jeśli występują problemy z wariancję obciążeń działających na dysk twardy rozwiązań w środowisku lokalnym. Standardowe dyski SSD dostarczać lepsze dostępności, spójności, niezawodności i opóźnienia w porównaniu do dysków HDD i nadają się dla serwerów sieci Web, niski serwerów aplikacji operacje We/Wy, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i obciążeń deweloperskich/testowych.

## <a name="standard-ssd-features"></a>Funkcje SSD w warstwie standardowa

**Usługa Managed Disks**: standardowe dyski SSD są dostępne tylko jako dyski Managed Disks. Niezarządzanych dysków i stronicowych obiektów blob nie są obsługiwane na SSD w warstwie standardowa. Podczas tworzenia dysku zarządzanego, określ typ dysku SSD w warstwie standardowa i wskazać rozmiar dysku należy i platforma Azure utworzy i zarządza dysku.
Standardowe dyski SSD obsługuje wszystkich operacji zarządzania usługi oferowane przez dyski Managed Disks. Na przykład można utworzyć, skopiować lub migawki dysków SSD zarządzane dyski w warstwie standardowa w taki sam sposób, możesz zrobić z dysków zarządzanych.

**Maszyny wirtualne**: standardowe dyski SSD, mogą być używane z wszystkich maszyn wirtualnych platformy Azure, w tym typy maszyn wirtualnych, które nie obsługują dysków w warstwie Premium. Na przykład, jeśli używasz maszyn wirtualnych serii A, lub maszyn wirtualnych serii N lub serii DS lub innych serii maszyny Wirtualnej platformy Azure, można użyć standardowych dysków SSD przy użyciu tej maszyny Wirtualnej. Wraz z wprowadzeniem SSD w warstwie standardowa udostępniliśmy szerokiej gamy obciążeń, które wcześniej używano dysków opartych na dysk twardy w celu przejścia do dysków na dyskach SSD i spójną wydajność, wyższą dostępność, lepsze opóźnienia i lepsze ogólnej środowisko, które są dostarczane z dysków SSD.

**Duża trwałość i wysoka dostępność**: standardowe dyski SSD są oparte na tę samą platformę Azure Disks ciągle dostarczał wysoką dostępność i niezawodność dysków. Dyski platformy Azure zaprojektowano z gwarancją dostępności na poziomie 99,999%. Podobnie jak wszystkie dyski zarządzane standardowe dyski SSD będzie także oferować lokalny magazyn nadmiarowy (LRS). Dzięki magazynowi LRS, platforma obsługuje wiele replik danych dla każdego dysku i ciągle dostarczał przeznaczonych dla przedsiębiorstw dzięki wiodącej w branży trwałość IaaS dysków ZERO procent wycena współczynnik błędów.

**Migawki**: jak wszystkie dyski Managed Disks, standardowe dyski SSD obsługują także tworzenie migawek. Standardowa (HDD) lub Premium (SSD), może być typu migawka. Oszczędności kosztów, zalecamy typ migawki z standardowa (HDD) dla wszystkich typów dysków platformy Azure. To dlatego podczas tworzenia dysku zarządzanego z migawki, zawsze jesteś w stanie wybrać warstwy wyższej, np. SSD w warstwie standardowa lub Premium SSD.

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności

Poniższa tabela zawiera rozmiary dysków, które są obecnie oferowane dla SSD w warstwie standardowa.

|Typ dysku SSD w warstwie standardowa  |Rozmiar dysku  |Operacje We/Wy na dysk  |Przepływność na dysk  |
|---------|---------|---------|---------|
|E10     |128 GiB         |Maks. 500         |MiB maksymalnie 60 na sekundę         |
|E15     |256 GiB         |Maks. 500         |MiB maksymalnie 60 na sekundę         |
|E20     |512 GiB         |Maks. 500         |MiB maksymalnie 60 na sekundę         |
|E30     |1024 giB         |Maks. 500         |MiB maksymalnie 60 na sekundę         |
|E40     |2048 giB         |Maks. 500         |MiB maksymalnie 60 na sekundę         |
|E50     |4095 giB         |Maks. 500         |MiB maksymalnie 60 na sekundę         |

Standardowe dyski SSD są przeznaczone zapewnienie milisekundowe opóźnienia w przypadku większości operacji We/Wy i operacje We/Wy i Przepływność w granicach opisano w powyższej tabeli. Rzeczywiste operacje We/Wy i przepływność będzie zależeć czasami w zależności od wzorców ruchu. Standardowe dyski SSD będą oferują bardziej spójną wydajność niż dyski HDD, z mniejszymi opóźnieniami.

Dyski SSD w warstwie Premium z drugiej strony, mają lepszą wydajność niż standardowe dyski SSD, z niskimi opóźnieniami, wysoka operacje We/Wy/przepływność i jeszcze lepszą zgodność z aprowizowaną wydajność dysku. To typ dysku zalecane dla obciążeń produkcyjnych krytycznych. Jeśli obciążenie wymaga obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach, należy rozważyć użycie magazynu Premium Storage.

Np. dyski Premium SSD standardowe dyski SSD Użyj rozmiar jednostki we/wy 256 KiB. Transferowanych danych jest mniejsza niż 256 KiB, jest uznawane za 1 jednostkę operacji We/Wy. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KiB. Na przykład 1100 operacji We/Wy KiB jest traktowana jako pięć jednostki we/wy.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Jeśli przy użyciu standardowych dysków SSD, zastosuj następujące zagadnienia dotyczące rozliczeń:

- Rozmiar dysku zarządzanego
- Migawki
- Wychodzące transfery danych
- Transakcje

**Rozmiar dysku zarządzanego**: opłaty za dyski zarządzane są naliczane zarezerwowany rozmiar. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej oferty rozmiaru dysku. Aby uzyskać szczegóły dotyczące rozmiarów dysków oferowane zobacz tabelę w powyższej sekcji cele skalowalności i wydajności. Każdy dysk mapuje rozmiar dysku obsługiwane i rozliczane w związku z tym. Na przykład, jeśli aprowizowanej 200 GiB SSD w warstwie standardowa go będzie zmapowana do oferty rozmiar dysku E15 (256 GiB). Rozliczenia dla dowolnego dysku jest proporcjonalnie do liczby godzin przy użyciu miesięczna oferta usługi Premium Storage. Na przykład jeśli zainicjowano obsługę administracyjną E10 dysku, a następnie usunięte 20 godzin, są naliczane dla oferty E10 jest naliczany proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku.

**Migawki**: migawek Managed Disks są rozliczane pojemności posługują się migawki, w docelowej i źródłowej, jeśli istnieje. Aby uzyskać więcej informacji na temat migawek, zobacz [migawki dysków zarządzanych](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Wychodzące transfery danych**: [wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

**Transakcje**: podobnie do standardowych dysków Twardych, transakcje na standardowych dyskach SSD Naliczanie opłat. Transakcje obejmują odczytu i zapisu na dysku. Rozmiar jednostki we/wy, używany do wyceny transakcji na SSD w warstwie standardowa jest 256 KiB. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KiB.

Aby uzyskać więcej informacji na temat cen dla maszyn wirtualnych i dyski Managed Disks zobacz:

- [Ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Ceny usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat tworzenia standardowych dysków SSD, zapoznaj się z przykładu, który pokazuje, jak utworzyć maszynę Wirtualną przy użyciu wielu standardowych dysków SSD.

> [!div class="nextstepaction"]
> [Tworzenie maszyny Wirtualnej przy użyciu wielu standardowych dysków SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)