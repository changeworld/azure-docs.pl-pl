---
title: Wybieranie poprawnej jednostki SKU maszyny Wirtualnej dla klastra usługi Azure Data Explorer
description: W tym artykule opisano sposób wybierania optymalnego rozmiaru jednostki SKU dla klastra usługi Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561854"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Wybierz właściwą jednostkę SKU maszyny Wirtualnej dla klastra usługi Azure Data Explorer 

Podczas tworzenia nowego klastra lub optymalizacji klastra pod kątem zmieniającego się obciążenia, Usługa Azure Data Explorer oferuje wiele jednostek SKU maszyny wirtualnej do wyboru. Maszyny wirtualne zostały starannie dobrane, aby zapewnić najbardziej optymalny koszt dla każdego obciążenia. 

Rozmiar i jednostka SKU maszyny Wirtualnej klastra zarządzania danymi są w pełni zarządzane przez usługę Azure Data Explorer. Są one określane przez takie czynniki, jak rozmiar maszyny Wirtualnej aparatu i obciążenie pozyskiwania. 

Jednostkę SKU maszyny Wirtualnej dla klastra aparatów można zmienić w dowolnym momencie, [skalując klaster](manage-cluster-vertical-scaling.md). Najlepiej zacząć od najmniejszego rozmiaru jednostki SKU, który pasuje do początkowego scenariusza. Należy pamiętać, że skalowanie w górę klastra powoduje przestój do 30 minut, podczas gdy klaster jest ponownie tworzony przy nowych jednostek SKU maszyny Wirtualnej.

> [!TIP]
> Obliczenia [wystąpienia zarezerwowane (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) ma zastosowanie do klastra usługi Azure Data Explorer.  

W tym artykule opisano różne opcje jednostek SKU maszyny Wirtualnej i zawiera szczegóły techniczne, które mogą pomóc w dokonyjuj najlepszego wyboru.

## <a name="select-a-cluster-type"></a>Wybieranie typu klastra

Usługa Azure Data Explorer oferuje dwa typy klastrów:

* **Produkcja:** Klastry produkcyjne zawierają dwa węzły dla klastrów aparatu i zarządzania danymi i są obsługiwane w ramach [umowy SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)usługi Azure Data Explorer.

* **Dev/Test (bez umowy SLA)**: Klastry deweloperów/testów mają jeden węzeł D11 v2 dla klastra aparatów i jeden węzeł D1 dla klastra zarządzania danymi. Ten typ klastra jest konfiguracją o najniższym koszcie ze względu na niską liczbę wystąpień i brak opłaty za narzucie narzutów aparatu. Nie ma umowy SLA dla tej konfiguracji klastra, ponieważ brakuje nadmiarowości.

## <a name="sku-types"></a>Typy jednostek SKU

Podczas tworzenia klastra usługi Azure Data Explorer wybierz *optymalną* jednostkę SKU maszyny Wirtualnej dla planowanego obciążenia. Możesz wybrać jedną z następujących dwóch rodzin jednostek SKU usługi Azure Data Explorer:

* **D v2:** Jednostka SKU D jest zoptymalizowana pod kątem obliczeń i jest dostępna w dwóch wersjach:
    * Sama maszyna wirtualna
    * Maszyna wirtualna w pakiecie z dyskami magazynu premium

* **LS:** Jednostka SKU L jest zoptymalizowana pod kątem pamięci masowej. Ma znacznie większy rozmiar SSD niż podobnie wyceniona jednostka SKU D.

Główne różnice między dostępnymi typami jednostek SKU opisano w poniższej tabeli:
 
| Atrybut | D SKU | L SKU |
|---|---|---
|**Małe jednostki SKU**|Minimalny rozmiar to D11 z dwoma rdzeniami|Minimalny rozmiar to L4 z czterema rdzeniami |
|**Dostępność**|Dostępne we wszystkich regionach (wersja DS+PS ma bardziej ograniczoną dostępność)|Dostępne w kilku regionach |
|**Koszt&nbsp;pamięci podręcznej GB na rdzeń**|Wysoka z jednostką SKU D, niska w wersji DS+PS|Najniższa z opcją Płatności zgodnie z rzeczywistym i wyjazdem |
|**Cennik wystąpienia zarezerwowanego (RI)**|Wysoka zniżka (ponad&nbsp;55 procent za trzyletnie zobowiązanie)|Niższa zniżka&nbsp;(20 procent za trzyletnie zobowiązanie) |  

## <a name="select-your-cluster-vm"></a>Wybieranie maszyny wirtualnej klastra 

Aby wybrać maszynę wirtualną klastra, [należy skonfigurować skalowanie w pionie](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Za pomocą różnych opcji SKU maszyny Wirtualnej do wyboru, można zoptymalizować koszty dla wydajności i hot-cache wymagania dla scenariusza. 
* Jeśli potrzebujesz najbardziej optymalnej wydajności dla dużej liczby zapytań, idealną jednostkę SKU należy zoptymalizować pod kątem obliczeń. 
* Jeśli trzeba wysyłać zapytania do dużych ilości danych przy stosunkowo mniejszym obciążeniu zapytań, jednostka SKU zoptymalizowana pod kątem magazynu może pomóc obniżyć koszty i nadal zapewniać doskonałą wydajność.

Ponieważ liczba wystąpień na klaster dla małych jednostek SKU jest ograniczona, zaleca się użycie większych maszyn wirtualnych, które mają większą pamięć RAM. Więcej pamięci RAM jest potrzebne dla niektórych typów zapytań, które `joins`stawiają większe zapotrzebowanie na zasób pamięci RAM, takich jak kwerendy, które używają . W związku z tym podczas rozważania opcji skalowania, zaleca się skalowanie do większej jednostki SKU, a nie skalowanie w poziomie przez dodanie większej liczby wystąpień.

## <a name="vm-options"></a>Opcje maszyny Wirtualnej

Specyfikacje techniczne maszyn wirtualnych klastra usługi Azure Data Explorer są opisane w poniższej tabeli:

|**Nazwa**| **Kategoria** | **Rozmiar SSD** | **Rdzenie** | **Pamięci ram** | **Dyski pamięci masowej&nbsp;w wersji premium (1 TB)**| **Minimalna liczba wystąpień na klaster** | **Maksymalna liczba wystąpień na klaster**
|---|---|---|---|---|---|---|---
|D11 v2| zoptymalizowane pod kątem obliczeń | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (z wyjątkiem jednostki SKU dewelopera/testowego, która wynosi 1)
|D12 v2| zoptymalizowane pod kątem obliczeń | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| zoptymalizowane pod kątem obliczeń | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1000
|D14 v2| zoptymalizowane pod kątem obliczeń | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem pamięci masowej | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem pamięci masowej | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem pamięci masowej | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem pamięci masowej | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1000
|L4s v1| zoptymalizowane pod kątem pamięci masowej | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| zoptymalizowane pod kątem pamięci masowej | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1000
|L16s_1| zoptymalizowane pod kątem pamięci masowej | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1000

* Zaktualizowaną listę jednostek SKU maszyny Wirtualnej na region można wyświetlić za pomocą interfejsu API Azure Data Explorer [ListSkus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Dowiedz się więcej o [różnych jednostkach SKU](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Następne kroki

* Można [skalować w górę lub w dół](manage-cluster-vertical-scaling.md) klastra aparatów w dowolnym momencie, zmieniając jednostkę SKU maszyny Wirtualnej, w zależności od różnych potrzeb. 

* Można [skalować lub skalować w poziomie](manage-cluster-horizontal-scaling.md) rozmiar klastra aparatów, aby zmienić pojemność, w zależności od zmieniających się wymagań.

