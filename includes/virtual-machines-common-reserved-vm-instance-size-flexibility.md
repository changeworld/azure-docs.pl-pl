---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471463"
---
Kupując wystąpienie zarezerwowanej maszyny Wirtualnej, można zoptymalizować dla elastyczności rozmiaru wystąpienia lub priorytetu pojemności. Aby uzyskać więcej informacji na temat ustawiania lub zmieniania ustawienia optymalizacji dla zarezerwowanych wystąpień maszyn wirtualnych, zobacz [Zmienianie ustawienia optymalizacji dla zarezerwowanych wystąpień maszyn wirtualnych](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

W przypadku wystąpienia zarezerwowanej maszyny wirtualnej, które jest zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia, zakupiona rezerwacja może dotyczyć rozmiarów maszyn wirtualnych (VM) w tej samej grupie elastyczności rozmiaru wystąpienia. Na przykład jeśli kupisz rezerwację dla rozmiaru maszyny Wirtualnej, który jest wymieniony w serii DSv2, takich jak Standard_DS5_v2, rabat rezerwacji może mieć zastosowanie do pozostałych czterech rozmiarów, które są wymienione w tej samej grupie elastyczności rozmiaru wystąpienia:

- Standardowa_DS1_v2
- Standardowa_DS2_v2
- Standardowa_DS3_v2
- Standardowa_DS4_v2

Ale rabat rezerwacji nie ma zastosowania do rozmiarów maszyn wirtualnych, które są wymienione w różnych grupach elastyczności rozmiaru wystąpień, takich jak jednostki SKU w wysokiej pamięci serii DSv2: Standard_DS11_v2, Standard_DS12_v2 i tak dalej.

W ramach grupy elastyczności rozmiaru wystąpienia liczba maszyn wirtualnych, których dotyczy rabat rezerwacji, zależy od rozmiaru maszyny Wirtualnej, który zostanie wybrany podczas zakupu rezerwacji. Zależy to również od rozmiarów maszyn wirtualnych, które są uruchomione. Kolumna ratio porównuje względny footprint dla każdego rozmiaru maszyny Wirtualnej w tej grupie elastyczności rozmiaru wystąpienia. Użyj wartości współczynnika, aby obliczyć, jak rabat rezerwacji ma zastosowanie do maszyn wirtualnych, które masz uruchomione.

## <a name="examples"></a>Przykłady

W poniższych przykładach użyto rozmiarów i współczynników w tabeli serii DSv2.

Zakup zarezerwowane wystąpienie maszyny Wirtualnej o rozmiarze Standard_DS4_v2, w którym stosunek lub względny ślad w porównaniu do innych rozmiarów w tej serii wynosi 8.

- Scenariusz 1: Uruchom osiem maszyn wirtualnych o Standard_DS1_v2 rozmiarze ze współczynnikiem 1. Zniżka na rezerwację dotyczy wszystkich ośmiu tych maszyn wirtualnych.
- Scenariusz 2: Uruchom dwie maszyny wirtualne o Standard_DS2_v2 rozmiarze ze współczynnikiem 2 każdy. Uruchom również maszynę wirtualną o Standard_DS3_v2 rozmiarze ze współczynnikiem 4. Całkowita powierzchnia to 2+2+4=8. Tak więc rabat na rezerwację dotyczy wszystkich trzech tych maszyn wirtualnych.
- Scenariusz 3: Uruchom jeden Standard_DS5_v2 ze współczynnikiem 16. Zniżka na rezerwację dotyczy połowy kosztu obliczeń maszyny Wirtualnej.

W poniższych sekcjach pokazano, jakie rozmiary znajdują się w tej samej grupie serii rozmiarów, gdy kupujesz zarezerwowane wystąpienie maszyny Wirtualnej zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Współczynnik elastyczności rozmiaru wystąpienia dla maszyn wirtualnych 

CSV poniżej ma grupy elastyczności rozmiaru wystąpienia, ArmSkuName i współczynniki.  

[Współczynniki elastyczności rozmiaru wystąpienia](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Zachowamy adres URL pliku i schemat naprawiony, dzięki czemu można używać tego pliku programowo. Dane będą również dostępne za pośrednictwem interfejsu API wkrótce.
