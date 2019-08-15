---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: a98a941477fc83a104b55ed91f457c5a48f90d59
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029837"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych

W przypadku wystąpienia zarezerwowanej maszyny wirtualnej, która jest zoptymalizowana pod kątem elastyczności rozmiaru wystąpienia, rezerwacja kupowana może być stosowana do rozmiarów maszyn wirtualnych w tej samej elastycznej grupie o rozmiarze. Jeśli na przykład zakupisz rezerwację dla rozmiaru maszyny wirtualnej, która jest wymieniona w serii DSv2, np. Standard_DS5_v2, Rabat rezerwacji może dotyczyć innych czterech rozmiarów, które są wymienione w tej samej elastycznej grupie o rozmiarze wystąpienia:

- Standardowa_DS1_v2
- Standardowa_DS2_v2
- Standardowa_DS3_v2
- Standardowa_DS4_v2

Jednak rabat rezerwacji nie ma zastosowania do rozmiarów maszyn wirtualnych, które są wymienione w różnych elastycznych rozmiarach, takich jak jednostki SKU w DSv2 z dużą ilością pamięci: Standard_DS11_v2, Standard_DS12_v2 i tak dalej.

W grupie elastycznej rozmiaru wystąpienia liczba maszyn wirtualnych, których dotyczy rabat rezerwacji, zależy od rozmiaru maszyny wirtualnej, który został wybrany podczas zakupu rezerwacji. Zależy to również od rozmiaru maszyn wirtualnych, które są uruchomione. Kolumna współczynnik porównuje względne rozmiary dla każdego rozmiaru maszyny wirtualnej w grupie elastycznej rozmiaru tego wystąpienia. Użyj wartości współczynnika, aby obliczyć, w jaki sposób rabat rezerwacji ma zastosowanie do maszyn wirtualnych, które są uruchomione.

## <a name="examples"></a>Przykłady

W poniższych przykładach używane są rozmiary i wskaźniki w tabeli serii DSv2.

Zakup wystąpienia zarezerwowanego maszyny wirtualnej o rozmiarze Standard_DS4_v2 w przypadku, gdy stosunek lub względna wartość jest porównywany z innymi rozmiarami w tej serii, wynosi 8.

- Scenariusz 1: Uruchom osiem Standard_DS1_v2 rozmiary maszyn wirtualnych o współczynniku 1. Rabat związany z rezerwacją dotyczy wszystkich ośmiu z tych maszyn wirtualnych.
- Scenariusz 2: Uruchom dwie maszyny wirtualne Standard_DS2_v2 o rozmiarze z zastosowaniem współczynnika 2 każdego. Należy również uruchomić maszynę wirtualną o rozmiarze Standard_DS3_v2 z zastosowaniem współczynnika 4. Całkowite rozmiary to 2 + 2 + 4 = 8. W związku z tym rabat dla rezerwacji ma zastosowanie do wszystkich trzech tych maszyn wirtualnych.
- Scenariusz 3: Uruchom jeden Standard_DS5_v2 z stosunkiem 16. Rabat związany z rezerwacją dotyczy połowy kosztu obliczeniowego maszyny wirtualnej.

W poniższych sekcjach przedstawiono, jakie rozmiary znajdują się w tej samej grupie serii rozmiarów, gdy kupowane jest wystąpienie zarezerwowane maszyny wirtualnej zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Stopień elastyczności rozmiaru wystąpienia maszyn wirtualnych 

Poniższy wolumin CSV ma elastyczne grupy o rozmiarze wystąpienia, ArmSkuName i współczynniki.  

[Współczynniki elastyczności rozmiaru wystąpienia](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Zachowamy adres URL pliku i schemat, aby można było programowo wykorzystać ten plik. Dane będą również dostępne za pomocą interfejsu API wkrótce.
