---
title: Wybierz poprawną jednostkę SKU maszyny wirtualnej dla klastra usługi Azure Eksplorator danych
description: W tym artykule opisano, jak wybrać optymalny rozmiar jednostki SKU dla klastra usługi Azure Eksplorator danych.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2eb23a65196ac4f6456f50dbbbfd9e4b484ad171
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515731"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Wybierz poprawną jednostkę SKU maszyny wirtualnej dla klastra usługi Azure Eksplorator danych 

Podczas tworzenia nowego klastra lub optymalizowania klastra w celu zmiany obciążenia usługa Azure Eksplorator danych oferuje wiele jednostek SKU maszyn wirtualnych, spośród których można wybierać. Maszyny wirtualne zostały starannie wybrane, aby zapewnić optymalny koszt dla każdego obciążenia. 

Rozmiar i jednostka SKU maszyny wirtualnej klastra zarządzania danymi są w pełni zarządzane przez usługę Eksplorator danych platformy Azure. Są one określane według takich czynników, jak rozmiar maszyny wirtualnej aparatu i obciążenie pozyskiwania. 

Możesz w dowolnym momencie zmienić jednostkę SKU maszyny wirtualnej dla klastra aparatu, przeskalując [w górę klaster](manage-cluster-vertical-scaling.md). Najlepiej zacząć od najmniejszego rozmiaru jednostki SKU, który pasuje do scenariusza początkowego. Należy pamiętać, że skalowanie w górę klastra powoduje przestoje nawet do 30 minut, podczas gdy klaster jest odtwarzany przy użyciu nowej jednostki SKU maszyny wirtualnej.

> [!TIP]
> Zarezerwowane wystąpienia obliczeniowe [(ri)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) mają zastosowanie do klastra usługi Azure Eksplorator danych.  

W tym artykule opisano różne opcje jednostki SKU maszyny wirtualnej i przedstawiono szczegółowe informacje techniczne, które mogą pomóc w wyborze najlepszego wyboru.

## <a name="select-a-cluster-type"></a>Wybierz typ klastra

Usługa Azure Eksplorator danych oferuje dwa typy klastrów:

* **Produkcji**: Klastry produkcyjne zawierają dwa węzły klastrów i zarządzania danymi, które są obsługiwane w ramach [umowy SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)Eksplorator danych platformy Azure.

* Tworzenie **i testowanie (bez umowy SLA)** : Klastry deweloperskie/testowe mają jeden węzeł D11 v2 dla klastra aparatów i jeden węzeł D1 dla klastra zarządzania danymi. Ten typ klastra to najniższa konfiguracja kosztu ze względu na jego niską liczbę wystąpień i brak opłaty za znakowanie silnika. Nie ma umowy SLA dla tej konfiguracji klastra, ponieważ nie ma ona nadmiarowości.

## <a name="sku-types"></a>Typy jednostek SKU

Podczas tworzenia klastra usługi Azure Eksplorator danych wybierz optymalną jednostkę SKU maszyny wirtualnej dla planowanego obciążenia. Można wybrać jedną z następujących dwóch rodzin jednostek SKU Eksplorator danych platformy Azure:

* **D v2**: Jednostka SKU D jest zoptymalizowana pod kątem obliczeń i występuje w dwóch wersjach:
    * Sama maszyna wirtualna
    * Maszyna wirtualna z dyskami magazynu Premium Storage

* **LS**: Jednostka SKU L jest zoptymalizowana pod kątem magazynu. Ma znacznie większy rozmiar SSD niż w przypadku jednostki SKU o podobnej cenie D.

Kluczowe różnice między dostępnymi typami jednostek SKU zostały opisane w poniższej tabeli:
 
| Atrybut | JEDNOSTKA SKU D | JEDNOSTKA SKU L |
|---|---|---
|**Małe jednostki SKU**|Minimalny rozmiar jest D11 z dwoma rdzeniami|Minimalny rozmiar to P4 z czterema rdzeniami |
|**Dostępność**|Dostępne we wszystkich regionach (wersja DS + PS ma więcej ograniczonej dostępności)|Dostępne w kilku regionach |
|**Koszt pamięci podręcznej wGBnardzeń&nbsp;**|Wysoka z jednostkami SKU D, niska w wersji DS + PS|Najniższy z opcją płatność zgodnie z rzeczywistym użyciem |
|**Cennik wystąpień zarezerwowanych (RI)**|Wysoki rabat (ponad 55&nbsp;% dla zobowiązania z trzech lat)|Niższy rabat (20&nbsp;procent dla zobowiązania z trzech lat) |  

## <a name="select-your-cluster-vm"></a>Wybierz maszynę wirtualną klastra 

Aby wybrać maszynę wirtualną klastra, [Skonfiguruj skalowanie](manage-cluster-vertical-scaling.md#configure-vertical-scaling)w pionie. 

Korzystając z różnych opcji jednostki SKU maszyny wirtualnej do wyboru, można zoptymalizować koszty dotyczące wymagań dotyczących wydajności i pamięci podręcznej w danym scenariuszu. 
* Jeśli potrzebujesz optymalnej wydajności dla dużej ilości zapytań, idealna jednostka SKU powinna być zoptymalizowana pod kątem obliczeń. 
* Jeśli trzeba wykonać zapytanie o duże ilości danych przy stosunkowo niższym obciążeniu zapytania, jednostka SKU zoptymalizowana pod kątem magazynu może pomóc w obniżeniu kosztów i nadal zapewniać doskonałą wydajność.

Ponieważ liczba wystąpień na klaster dla małych jednostek SKU jest ograniczona, preferowane jest użycie większych maszyn wirtualnych z większą ilością pamięci RAM. Wymagana jest większa ilość pamięci RAM dla niektórych typów zapytań, które zwiększają zapotrzebowanie na zasób pamięci RAM, takie jak `joins`zapytania, które używają. W związku z tym podczas rozważania opcji skalowania zalecamy skalowanie w górę do większej jednostki SKU, a nie skalowanie przez dodanie większej liczby wystąpień.

## <a name="vm-options"></a>Opcje maszyny wirtualnej

Specyfikacje techniczne dotyczące maszyn wirtualnych klastra usługi Azure Eksplorator danych są opisane w poniższej tabeli:

|**Nazwa**| **Kategoria** | **Rozmiar SSD** | **Rdzeni** | **RAM** | **Dyski magazynu Premium Storage (&nbsp;1 TB)**| **Minimalna liczba wystąpień na klaster** | **Maksymalna liczba wystąpień na klaster**
|---|---|---|---|---|---|---|---
|D11 v2| zoptymalizowane pod kątem obliczeń | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (z wyjątkiem jednostki SKU Dev/Test, która jest 1)
|D12 v2| zoptymalizowane pod kątem obliczeń | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| zoptymalizowane pod kątem obliczeń | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1000
|D14 v2| zoptymalizowane pod kątem obliczeń | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem magazynu | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1000
|DS13 V2 + 2&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem magazynu | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem magazynu | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| zoptymalizowane pod kątem magazynu | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1000
|L4s v1| zoptymalizowane pod kątem magazynu | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| zoptymalizowane pod kątem magazynu | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1000
|L16s_1| zoptymalizowane pod kątem magazynu | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1000

* Zaktualizowaną listę jednostek SKU maszyny wirtualnej można wyświetlić za pomocą [interfejsu API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)usługi Azure Eksplorator danych ListSkus. 
* Dowiedz się więcej o różnych jednostkach [SKU obliczeniowych](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Następne kroki

* Klaster silnik można [skalować w górę lub w dół](manage-cluster-vertical-scaling.md) w dowolnym momencie, zmieniając jednostkę SKU maszyny wirtualnej w zależności od różnych potrzeb. 

* W zależności od zmieniających się wymagań można [skalować lub skalować](manage-cluster-horizontal-scaling.md) rozmiar klastra silnikowego w celu zmiany pojemności.

