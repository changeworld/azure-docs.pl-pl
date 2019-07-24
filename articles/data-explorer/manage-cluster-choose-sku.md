---
title: Wybierz poprawną jednostkę SKU maszyny wirtualnej dla klastra usługi Azure Eksplorator danych
description: W tym artykule opisano, jak wybrać optymalny rozmiar jednostki SKU dla klastra usługi Azure Eksplorator danych.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226150"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Wybierz poprawną jednostkę SKU maszyny wirtualnej dla klastra usługi Azure Eksplorator danych 

Usługa Azure Eksplorator danych ma wiele jednostek SKU maszyn wirtualnych, które można wybrać podczas tworzenia nowego klastra lub optymalizowania klastra w celu zmiany obciążenia. Maszyny wirtualne zostały starannie wybrane, aby zapewnić optymalny koszt obciążeń. 

Jednostka SKU rozmiaru i maszyny wirtualnej klastra zarządzania danymi jest w pełni zarządzana przez usługę Eksplorator danych platformy Azure. Jest określana na podstawie czynników, takich jak rozmiar maszyny wirtualnej aparatu i obciążenie pozyskiwania. 

Jednostkę SKU maszyny wirtualnej dla klastra aparatu można zmienić w dowolnym momencie, [skalując w górę klaster](manage-cluster-vertical-scaling.md). W związku z tym najlepiej zacząć od minimalnej wielkości jednostki SKU, która pasuje do scenariusza początkowego. Należy pamiętać, że skalowanie w górę klastra powoduje przestoje przez maksymalnie 30 minut, podczas gdy klaster jest odtwarzany przy użyciu nowej jednostki SKU maszyny wirtualnej.

> [!TIP]
> Obliczenia [RI (wystąpienia zarezerwowane)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) mają zastosowanie do klastra usługi Azure Eksplorator danych.  

W tym artykule opisano różne opcje jednostki SKU maszyny wirtualnej i przedstawiono szczegółowe informacje techniczne, które mogą ułatwić optymalne wybór.

## <a name="select-the-cluster-type"></a>Wybierz typ klastra

Usługa Azure Eksplorator danych oferuje dwa typy klastrów:

* **Produkcji**: Klastry produkcyjne zawierają dwa węzły klastrów i zarządzania danymi, które są obsługiwane w ramach [umowy SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)Eksplorator danych platformy Azure.

* Tworzenie **i testowanie (bez umowy SLA)** : Klastry deweloperskie/testowe mają jeden węzeł D11_v2 dla klastra aparatów i jeden węzeł D1 dla klastra zarządzania danymi. Ten typ klastra to najniższa konfiguracja kosztu z powodu niewielkiej liczby wystąpień i opłaty za znakowanie silnika. Brak umowy SLA dla tej konfiguracji klastra, ponieważ nie ma ona nadmiarowości.

## <a name="sku-types"></a>Typy jednostek SKU

Podczas tworzenia klastra usługi Azure Eksplorator danych wybierz optymalną  jednostkę SKU maszyny wirtualnej dla planowanego obciążenia. Usługa Azure Eksplorator danych ma dwie rodziny jednostek SKU, z których można wybrać:

* **D_V2**: Jednostka SKU w wersji **D** jest zoptymalizowana pod kątem obliczeń i udostępniana w dwóch wersjach.
    * Sama maszyna wirtualna
    * Maszyna wirtualna z dyskami magazynu Premium Storage

* **LS**: Jednostka SKU **L** jest zoptymalizowana pod kątem magazynu. Ma znacznie większy rozmiar SSD niż podobna jednostka SKU **D** .

Poniższa tabela zawiera podstawowe różnice między dostępnymi typami jednostek SKU:
 
|**Przypisane** | **JEDNOSTKA SKU D** | **JEDNOSTKA SKU L**
|---|---|---
|**Małe jednostki SKU**|Minimalny rozmiar to 11 "z dwoma rdzeniami|Minimalny rozmiar to "P4" z czterema rdzeniami
|**Dostępność**|Dostępne we wszystkich regionach (wersja DS + PS ma więcej ograniczonej dostępności)|Dostępne w kilku regionach
|**Koszt pamięci podręcznej w GB na rdzeń**|Wysoka z jednostkami SKU D, niska w wersji DS + PS|Najtańsze z opcją *płatność zgodnie z rzeczywistym* użyciem
|**Ceny RI (zarezerwowane wystąpienia)**|Wysoki rabat (ponad 55% dla zobowiązania z trzech lat)|Niższy rabat (20% dla zobowiązania z trzech lat)  

## <a name="select-your-cluster-vm"></a>Wybierz maszynę wirtualną klastra 

Aby wybrać maszynę wirtualną klastra, [Skonfiguruj skalowanie](manage-cluster-vertical-scaling.md#configure-vertical-scaling)w pionie. 

Różne opcje jednostki SKU maszyny wirtualnej umożliwiają Optymalizowanie kosztów wymaganej wydajności i wymaganej pamięci podręcznej w żądanym scenariuszu. Jeśli scenariusz wymaga najwyższej wydajności dla woluminu o wysokiej wartości zapytania, idealna jednostka SKU powinna być zoptymalizowana pod kątem obliczeń. Z drugiej strony, jeśli scenariusz wymaga wykonania zapytania o duże ilości danych przy stosunkowo niższym obciążeniu zapytania, usługa Storage zoptymalizowana pod kątem magazynu obniży koszty przy jednoczesnym zapewnieniu doskonałej wydajności.

Liczba wystąpień na klaster dla małych jednostek SKU jest ograniczona, dlatego zaleca się użycie większych maszyn wirtualnych z większą ilością pamięci RAM. Rozmiar pamięci RAM jest wymagany dla niektórych typów zapytań, które zwiększają zapotrzebowanie na zasoby pamięci RAM, takie jak te zapytania, `joins`które używają. W związku z tym podczas rozważania opcji skalowania zaleca się skalowanie do większej jednostki SKU niż w poziomie przez dodanie większej liczby wystąpień.

## <a name="vm-options"></a>Opcje maszyny wirtualnej

W poniższej tabeli przedstawiono specyfikacje techniczne dla maszyn wirtualnych klastra Eksplorator danych platformy Azure:

|**Nazwa**| **Kategoria** | **Rozmiar SSD** | **Rdzeni** | **RAM** | **Dyski magazynu Premium Storage (1 TB)**| **Minimalna liczba wystąpień na klaster** | **Maksymalna liczba wystąpień na klaster**
|---|---|---|---|---|---|---|---
|D11_v2| Optymalizacja pod kątem obliczeń | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (z wyjątkiem jednostki SKU deweloperskiej/testowej, gdzie jest 1)
|D12_v2| Optymalizacja pod kątem obliczeń | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| Optymalizacja pod kątem obliczeń | 307 GB   | 8 | 56 GB | 0 | 2 | 1000
|D14_v2| Optymalizacja pod kątem obliczeń | 614 GB   | 16| 112 GB | 0 | 2 | 1000
|DS13_v2 + 1 TB PS| Zoptymalizowane pod kątem magazynu | 1 TB | 8 | 56 GB | 1 | 2 | 1000
|DS13_v2 + 2 TB PS| Zoptymalizowane pod kątem magazynu | 2 TB | 8 | 56 GB | 2 | 2 | 1000
|DS14_v2 + NADMIAROWE PS| Zoptymalizowane pod kątem magazynu | 3 TB | 16 | 112 GB | 2 | 2 | 1000
|DS14_v2 + 4 TB PS| Zoptymalizowane pod kątem magazynu | 4 TB | 16 | 112 GB | 4 | 2 | 1000
|L4s_v1| Zoptymalizowane pod kątem magazynu | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| Zoptymalizowane pod kątem magazynu | 1,3 TB | 8 | 64 GB | 0 | 2 | 1000
|L16s_1| Zoptymalizowane pod kątem magazynu | 2,6 TB | 16| 128 GB | 0 | 2 | 1000

* Wyświetlanie listy zaktualizowanych jednostek SKU maszyn wirtualnych na region przy użyciu [interfejsu API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)usługi Azure Eksplorator danych ListSkus. 
* Dowiedz się więcej o różnych jednostkach [SKU obliczeniowych](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Następne kroki

* Klaster silnikowy można [skalować w górę lub w dół](manage-cluster-vertical-scaling.md) w dowolnym momencie, zmieniając jednostkę SKU maszyny wirtualnej pod kątem różnych potrzeb. 

* Rozmiar klastra aparatu można [skalować w systemie i na zewnątrz](manage-cluster-horizontal-scaling.md) , aby zmienić pojemność, zmieniając wymagania.

