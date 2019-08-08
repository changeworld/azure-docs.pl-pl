---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857450"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych

W przypadku wystąpienia zarezerwowanej maszyny wirtualnej, która jest zoptymalizowana pod kątem elastyczności rozmiaru wystąpienia, rezerwacja, którą kupujesz, można zastosować do rozmiarów maszyn wirtualnych w tej samej grupie serii rozmiarów. Jeśli na przykład zakupisz rezerwację dla rozmiaru maszyny wirtualnej, która znajduje się w tabeli serii DSv2, np. Standard_DS5_v2, Rabat rezerwacji może dotyczyć innych czterech rozmiarów, które są wymienione w tej samej tabeli:

- Standardowa_DS1_v2
- Standardowa_DS2_v2
- Standardowa_DS3_v2
- Standardowa_DS4_v2

Jednak rabat rezerwacji nie ma zastosowania do rozmiarów maszyn wirtualnych, które są wymienione w różnych tabelach, jak w tabeli DSv2 o dużej ilości pamięci: Standard_DS11_v2, Standard_DS12_v2 i tak dalej.

W grupie serii rozmiarów liczba maszyn wirtualnych, których dotyczy rabat rezerwacji, zależy od wybranego rozmiaru maszyny wirtualnej podczas zakupu rezerwacji. Zależy to również od rozmiaru maszyn wirtualnych, które są uruchomione. Kolumna współczynnik, która jest wymieniona w poniższych tabelach, porównuje względne rozmiary dla każdego rozmiaru maszyny wirtualnej w tej grupie. Użyj wartości współczynnika, aby obliczyć, w jaki sposób rabat rezerwacji ma zastosowanie do maszyn wirtualnych, które są uruchomione.

## <a name="examples"></a>Przykłady

W poniższych przykładach używane są rozmiary i wskaźniki w tabeli serii DSv2.

 Zakup wystąpienia zarezerwowanego maszyny wirtualnej o rozmiarze Standard_DS4_v2 w przypadku, gdy stosunek lub względna wartość jest porównywany z innymi rozmiarami w tej serii, wynosi 8.

- Scenariusz 1: Uruchom osiem Standard_DS1_v2 rozmiary maszyn wirtualnych o współczynniku 1. Rabat związany z rezerwacją dotyczy wszystkich ośmiu z tych maszyn wirtualnych.
- Scenariusz 2: Uruchom dwie maszyny wirtualne Standard_DS2_v2 o rozmiarze z zastosowaniem współczynnika 2 każdego. Należy również uruchomić maszynę wirtualną o rozmiarze Standard_DS3_v2 z zastosowaniem współczynnika 4. Całkowite rozmiary to 2 + 2 + 4 = 8. W związku z tym rabat dla rezerwacji ma zastosowanie do wszystkich trzech tych maszyn wirtualnych.
- Scenariusz 3: Uruchom jeden Standard_DS5_v2 z stosunkiem 16. Rabat związany z rezerwacją dotyczy połowy kosztu obliczeniowego maszyny wirtualnej.

W poniższych sekcjach przedstawiono, jakie rozmiary znajdują się w tej samej grupie serii rozmiarów, gdy kupowane jest wystąpienie zarezerwowane maszyny wirtualnej zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia.

## <a name="b-series"></a>Seria B

| Size | Współczynnik|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych z serii B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Duża pamięć w serii B

| Size | Współczynnik|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych z serii B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Seria D

| Size | Współczynnik|
|---|---|
| Standardowa_D1|1|
|Standardowa_D2|2|
|Standardowa_D3|4|
|Standardowa_D4|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Duża pamięć serii D

| Size | Współczynnik|
|---|---|
| Standardowa_D11|1|
|Standardowa_D12|2|
|Standardowa_D13|4|
|Standardowa_D14|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Seria ds

| Size | Współczynnik|
|---|---|
|Standardowa_DS1|1|
|Standardowa_DS2|2|
|Standardowa_DS3|4|
|Standardowa_DS4|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Duża pamięć w serii ds

| Size | Współczynnik|
|---|---|
|Standardowa_DS11|1|
|Standardowa_DS12|2|
|Standardowa_DS13|4|
|Standardowa_DS14|8|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Seria DSv2

| Size | Współczynnik|
|---|---|
|Standardowa_DS1_v2|1|
|Standardowa_DS2_v2|2|
|Standardowa_DS3_v2|4|
|Standardowa_DS4_v2|8|
|Standardowa_DS5_v2|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Duża pamięć w serii DSv2

| Size | Współczynnik|
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
|Standard_DS15_v2|10|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>DSv2 dużej ilości pamięci izolowanej

| Size | Współczynnik|
|---|---|
|Standard_DS15i_v2|1|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>Seria DSv3

| Size | Współczynnik|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standardowa_D8s_v3|4|
|Standardowa_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych ogólnego przeznaczenia](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Seria Dv2

| Size | Współczynnik|
|---|---|
|Standardowa_D1_v2|1|
|Maszyna wirtualna Standard_D2_v2|2|
|Maszyna wirtualna Standard_D3_v2|4|
|Standardowa_D4_v2|8|
|Standardowa_D5_v2|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Duża pamięć w serii Dv2

| Size | Współczynnik|
|---|---|
|Standardowa_D11_v2|1|
|Standardowa_D12_v2|2|
|Standardowa_D13_v2|4|
|Standardowa_D14_v2|8|
|Standard_D15_v2|10|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Dv2 dużej ilości pamięci izolowanej

| Size | Współczynnik|
|---|---|
|Standard_D15i_v2|1|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Seria Dv3

| Size | Współczynnik|
|---|---|
| Maszyna wirtualna Standard_D2_v3|1|
|Maszyna wirtualna Standard_D4_v3|2|
|Standardowa_D8_v3|4|
|Standardowa_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych ogólnego przeznaczenia](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>Seria ESv3

| Size | Współczynnik|
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
|Standardowa_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>Seria ESv3 — seria izolowana

| Size | Współczynnik|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Seria Ev3

| Size | Współczynnik|
|---|---|
|Standardowa_E2_v3|1|
|Standardowa_E4_v3|2|
|Standardowa_E8_v3|4|
|Standardowa_E16_v3|8|
|Standard_E20_v3|10|
|Standardowa_E32_v3|16|
|Standardowa_E64_v3|32|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Seria EV3 — seria izolowana

| Size | Współczynnik|
|---|---|
|Standard_E64i_v3|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Seria F

| Size | Współczynnik|
|---|---|
| Standardowa_F1|1|
|Standardowa_F2|2|
|Standardowa_F4|4|
|Standardowa_F8|8|
Standardowa_F16|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Seria FS

| Size | Współczynnik|
|---|---|
| Standardowa_F1s|1|
|Standardowa_F2s|2|
|Standardowa_F4s|4|
|Standardowa_F8s|8|
|Standardowa_F16s|16|

Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Seria Fsv2

| Size | Współczynnik|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>Seria H

| Size | Współczynnik|
|---|---|
| Standardowa_H8|1|
|Standardowa_H16|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Duża pamięć w serii H

| Size | Współczynnik|
|---|---|
| Standardowa_H8m|1|
|Standardowa_H16m|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>Seria magazynu HCS

| Size | Współczynnik|
|---|---|
|Standard_HC44rs|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Seria HBS

| Size | Współczynnik|
|---|---|
|Standard_HB60rs|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Seria HBS

| Size | Współczynnik|
|---|---|
|Standard_HB60rs|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>Seria H — małe opóźnienia serii

| Size | Współczynnik|
|---|---|
|Standard_H16r|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>Duża ilość opóźnienia pamięci serii H — seria

| Size | Współczynnik|
|---|---|
|Standard_H16mr|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>Seria H

| Size | Współczynnik|
|---|---|
|Standardowa_H8|1|
|Standardowa_H16|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>Seria serii LSv2

| Size | Współczynnik|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>Seria M

| Size | Współczynnik|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Ułamek serii M

| Size | Współczynnik|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Duża ilość pamięci w serii M

| Size | Współczynnik|
|---|---|
|Standard_M8ms|1|
|Standard_M8 — 2 MS|1|
|Standard_M8 — 4 MS|1|
|Standard_M16ms|2|
|Standard_M16 — 4 MS|2|
|Standard_M16 — 8ms|2|
|Standard_M32ms|4|
|Standard_M32 — 8ms|4|
|Standard_M32 — 16ms|4|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Duże części serii M

| Size | Współczynnik|
|---|---|
|Standard_M32ls|1|
|Standard_M64ls|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Duża pamięć serii M

| Size | Współczynnik|
|---|---|
|Standardowa_M64ms|1|
|Standard_M64 — 16ms|1|
|Standard_M64 — 32ms|1|
|Standard_M128ms|2|
|Standard_M128 — 32ms|2|
|Standard_M128 — 64ms|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>Seria MS — ułamkowa, bardzo mała

| Size | Współczynnik|
|---|---|
|Standard_M32ls|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>Duża ilość pamięci serii MSv2

| Size | Współczynnik|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>Seria MSv2

| Size | Współczynnik|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Seria NC

| Size | Współczynnik|
|---|---|
|Standardowa_NC6|1|
|Standardowa_NC12|2|
|Standardowa_NC24|4|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Seria NCv2

| Size | Współczynnik|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Seria NCv3

| Size | Współczynnik|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Seria ND

| Size | Współczynnik|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Seria NV

| Size | Współczynnik|
|---|---|
| Standardowa_NV6|1|
|Standardowa_NV12|2|
|Standardowa_NV24|4|

## <a name="nvsv3-series"></a>Seria NVSv3

| Size | Współczynnik|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>Seria małych opóźnień w serii NDS

| Size | Współczynnik|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>Seria małych opóźnień w serii NCSv3

| Size | Współczynnik|
|---|---|
|Standard_NC24rs_v3|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>Seria małych opóźnień w serii NCSv2

| Size | Współczynnik|
|---|---|
|Standard_NC24rs_v2|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>Małe opóźnienia w serii NC — seria

| Size | Współczynnik|
|---|---|
|Standard_NC24r|1|

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).





