---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 05820cc5f7b7d61d83f73ea5b62b05f8712e0997
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156221"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych

Z wystąpieniem zastrzeżonej maszyny wirtualnej, która ma jest zoptymalizowana, na przykład rozmiar elastyczność zastrzeżenia, których można zakupić stosować je do rozmiarów maszyn wirtualnych (VM) w tej samej grupie rozmiar w serii. Na przykład jeśli kupić rezerwację dla rozmiaru maszyny Wirtualnej, który znajduje się w tabeli Seria DSv2, takich jak Standard_DS5_v2, rabat związany z rezerwacją można zastosować do czterech rozmiarów, które są wymienione w tej samej tabeli:

- Standardowa_DS1_v2
- Standardowa_DS2_v2
- Standardowa_DS3_v2
- Standardowa_DS4_v2

Jednak ten rabat związany z rezerwacją nie ma zastosowania do rozmiary maszyn wirtualnych, które są wymienione w różnych tabelach, takich jak co to jest w tabeli Seria DSv2 dużą ilość pamięci: Standard_DS11_v2 Standard_DS12_v2 i tak dalej.

W ramach grupy serii rozmiar liczbę rabat związany z rezerwacją ma zastosowanie do maszyn wirtualnych zależy od rozmiaru maszyny Wirtualnej, które można wybrać w przypadku dokonywania zakupu rezerwacji. Zależy on również rozmiary maszyn wirtualnych, które masz uruchomiony. Kolumny współczynnika, który znajduje się w poniższych tabelach przedstawiono porównanie względnej śladu dla każdego rozmiaru maszyny Wirtualnej w tej grupie. Użyj wartości współczynnika do obliczenia, jak rabat związany z rezerwacją ma zastosowanie do maszyn wirtualnych możesz zostać uruchomione.

## <a name="examples"></a>Przykłady

W poniższych przykładach używane rozmiary i współczynników w tabeli DSv2 serii.

 Możesz kupić wystąpienia zarezerwowanego maszyny Wirtualnej o rozmiarze Standard_DS4_v2, w którym współczynnik lub zużycie względne w porównaniu do innych rozmiarów z tej serii wynosi 8.

- Scenariusz 1: Uruchom ośmiu standardowa_ds1_v2 o rozmiarach maszyn wirtualnych przy użyciu współczynnika o wartości 1. Rabat związany z rezerwacją ma zastosowanie do ośmiu wszystkich tych maszyn wirtualnych.
- Scenariusz 2: Uruchom dwa Standard_DS2_v2 o rozmiarach maszyn wirtualnych o stosunku 2. Również uruchomić standardowa_ds3_v2 o rozmiarach maszyn wirtualnych o stosunku liczby 4. Całkowita śladu jest 2 + 2 + 4 = 8. Dlatego rabat związany z rezerwacją ma zastosowanie do wszystkich trzech tych maszyn wirtualnych.
- Scenariusz 3: Uruchom jeden Standard_DS5_v2 przy użyciu współczynnika 16. Rabat związany z rezerwacją dotyczy koszt obliczeń w wysokości równej połowie tej maszyny Wirtualnej.

W poniższych sekcjach pokazano, jakie rozmiary są w tej samej grupie serii rozmiarów, w przypadku dokonywania zakupu wystąpienia zarezerwowanego maszyny Wirtualnej zoptymalizowane pod kątem dla wystąpienia elastyczność rozmiaru.

## <a name="b-series"></a>Seria B

| Rozmiar | Współczynnik|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów z możliwością zwiększania wydajności maszyn wirtualnych serii B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Seria B dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Aby uzyskać więcej informacji, zobacz [rozmiarów z możliwością zwiększania wydajności maszyn wirtualnych serii B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Seria D

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_D1|1|
|Standardowa_D2|2|
|Standardowa_D3|4|
|Standardowa_D4|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Seria D dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_D11|1|
|Standardowa_D12|2|
|Standardowa_D13|4|
|Standardowa_D14|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Seria ds

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_DS1|1|
|Standardowa_DS2|2|
|Standardowa_DS3|4|
|Standardowa_DS4|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Seria ds dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_DS11|1|
|Standardowa_DS12|2|
|Standardowa_DS13|4|
|Standardowa_DS14|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Seria DSv2

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_DS1_v2|1|
|Standardowa_DS2_v2|2|
|Standardowa_DS3_v2|4|
|Standardowa_DS4_v2|8|
|Standardowa_DS5_v2|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Seria DSv2 dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standardowa_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standardowa_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standardowa_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standardowa_ds15_v2|10|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>Seria DSv3

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_D2s_v3|1|
|Standardowa_D4s_v3|2|
|Standardowa_D8s_v3|4|
|Standardowa_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych ogólnego przeznaczenia](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Seria Dv2

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_D1_v2|1|
|Standardowa_D2_v2|2|
|Standardowa_D3_v2|4|
|Standardowa_D4_v2|8|
|Standardowa_D5_v2|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Seria Dv2 dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_D11_v2|1|
|Standardowa_D12_v2|2|
|Standardowa_D13_v2|4|
|Standardowa_D14_v2|8|
|Maszyna wirtualna Standard_D15_v2|10|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv3-series"></a>Seria Dv3

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_D2_v3|1|
|Standardowa_D4_v3|2|
|Standardowa_D8_v3|4|
|Standardowa_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych ogólnego przeznaczenia](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>Seria ESv3

| Rozmiar | Współczynnik|
|---|---|
|Standardowa_E2s_v3|1|
|Standardowa_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standardowa_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standardowa_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standardowa_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standardowa_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Seria Ev3

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_E2_v3|1|
|Standardowa_E4_v3|2|
|Standardowa_E8_v3|4|
|Standardowa_E16_v3|8|
|Standard_E20_v3|10|
|Standardowa_E32_v3|16|
|Standardowa_E64_v3|32|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Seria F

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_F1|1|
|Standardowa_F2|2|
|Standardowa_F4|4|
|Standardowa_F8|8|
Standardowa_F16|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Seria FS

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_F1s|1|
|Standardowa_F2s|2|
|Standardowa_F4s|4|
|Standardowa_F8s|8|
|Standardowa_F16s|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Seria Fsv2

| Rozmiar | Współczynnik|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyny wirtualne oferujące zoptymalizowane możliwości obliczeniowe](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>Seria H

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_H8|1|
|Standardowa_H16|2|

Aby uzyskać więcej informacji, zobacz [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Seria H dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_H8m|1|
|Standardowa_H16m|2|

Aby uzyskać więcej informacji, zobacz [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Seria Ls

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_L4s|1|
|Standardowa_L8s|2|
|Standardowa_L16s|4|
|Standardowa_L32s|8|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyny wirtualnej zoptymalizowane pod kątem magazynu](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>Seria M

| Rozmiar | Współczynnik|
|---|---|
| Maszyna wirtualna Standard_M64s|1|
|Maszyna wirtualna Standard_M128s|2|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Seria M ułamkowych

| Rozmiar | Współczynnik|
|---|---|
| Warstwie standardowa_m16s|1|
|Warstwie standardowa_m32s|2|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Seria M ułamkowe dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
|Warstwie standardowa_m8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Warstwie standardowa_m16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Warstwie standardowa_m32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Seria M ułamkowe duże

| Rozmiar | Współczynnik|
|---|---|
| Warstwie standardowa_m32ls|1|
|Warstwie standardowa_m64ls|2|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Seria M dużą ilość pamięci

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Maszyna wirtualna Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Seria NC

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_NC6|1|
|Standardowa_NC12|2|
|Standardowa_NC24|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Seria NCv2

| Rozmiar | Współczynnik|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Seria NCv3

| Rozmiar | Współczynnik|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Seria ND

| Rozmiar | Współczynnik|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Seria NV

| Rozmiar | Współczynnik|
|---|---|
| Standardowa_NV6|1|
|Standardowa_NV12|2|
|Standardowa_NV24|4|

Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


