---
title: Rozmiary maszyn wirtualnych obsługiwanych w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Odwołanie do rozmiarów obsługiwanych maszyn wirtualnych w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 79d18f938dc51bb7eec62120e8bc6743cb2840c4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886475"
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Rozmiary maszyn wirtualnych obsługiwanych w usłudze Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W tym artykule wymieniono rozmiarów maszyn wirtualnych, które są dostępne w usłudze Azure Stack.

Dysk operacje We/Wy (dane wejściowe i wyjściowe operacji na sekundę) w usłudze Azure Stack zależy od rozmiaru maszyny wirtualnej (VM) zamiast typu dysku. Oznacza, że dla serii Standard_Fs maszyn wirtualnych, niezależnie od tego, czy wybrano dysk SSD lub HDD dla typu dysku limit operacji We/Wy dysku danych jednego dodatkowego 2300 operacje We/Wy. Operacje We/Wy ograniczenia nakładane jest ograniczenie (maksymalną liczbę kolumn) do hałaśliwym sąsiedztwem. Nie jest zapewnienie obsługi operacji We/Wy, które otrzymasz od określonego rozmiaru maszyny Wirtualnej.

## <a name="virtual-machine-general-purpose"></a>Maszyna wirtualna ogólnego przeznaczenia

Rozmiary maszyn wirtualnych ogólnego przeznaczenia udostępniają o zrównoważonym obciążeniu współczynnik procesora CPU w stosunku do pamięci. Są one używane do testowania i programowania, małych i średnich baz danych, oraz o niewielkim i średnim ruchu, serwery sieci web. Każdy dysk z danymi jest 2300 operacje We/Wy — wersja premium rozmiarów maszyn wirtualnych, z wyjątkiem Basic Z serii. Dla podstawowych A rozmiar dysku danych to 500 operacji We/Wy.

### <a name="basic-a"></a>Podstawowa A

> [!NOTE]
> *Podstawowa A* rozmiarów maszyn wirtualnych są wycofywane w przypadku [tworzenie zestawów skalowania maszyn wirtualnych](../azure-stack-compute-add-scalesets.md) (VMSS) za pośrednictwem portalu. Do utworzenia zestawu skalowania maszyn wirtualnych o tym rozmiarze, użyj programu PowerShell lub w szablonie.

|Size - Size\Name |Procesor wirtualny     |Memory (Pamięć) | Maksymalny rozmiar dysku tymczasowego | System operacyjny maksymalna przepływność dysków: (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna przepływność dysków danych (operacje We/Wy) | Maksymalna liczba kart sieciowych |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Podstawowa_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1x300  |1   |
|**A1\Podstawowa_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2 x 300  |1   |
|**A2\Podstawowa_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4x300  |1   |
|**A3\Podstawowa_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8x300  |1   |
|**A4\Podstawowa_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16 X 300 |1   |

### <a name="standard-a"></a>A standardowe 
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |    
|----------------|--|------|----|----|----|-------|---------|
|**Standardowa_A0** |1 |0,768 |20  |500 |500 |1 x 500  |1 |
|**Standardowa_A1** |1 |1,75  |70  |500 |500 |2 x 500  |1 |
|**Standardowa_A2** |2 |3,5   |135 |500 |500 |4 x 500  |1 |
|**Standardowa_A3** |4 |7     |285 |500 |500 |8 x 500  |2 |
|**Standardowa_A4** |8 |14    |605 |500 |500 |16 x 500 |4 |
|**Standardowa_A5** |2 |14    |135 |500 |500 |4 x 500  |2 |
|**Standardowa_A6** |4 |28    |285 |500 |500 |8 x 500  |2 |
|**Standardowa_A7** |8 |56    |605 |500 |500 |16 x 500 |4 |

### <a name="av2-series"></a>Seria Av2
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej*

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standardowa_A1_v2**  |1   |2   |10   |500 |1000  |2 / 2 x 500   |2 |
|**Standardowa_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4 x 500   |2 |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8 x 500   |4 |
|**Standardowa_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16 x 500 |8 |
|**Standardowa_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4 x 500   |2 |
|**Standardowa_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8 x 500   |4 |
|**Standardowa_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16 x 500 |8 |

### <a name="d-series"></a>Seria D
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|----------------|----|----|-----|----|------|------------|---------|
|**Standardowa_D1** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |1 |
|**Standardowa_D2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standardowa_D3** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standardowa_D4** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 |


### <a name="ds-series"></a>Seria DS
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standardowa_DS1** |1   |3,5 |7    |1000 |4000  |4 / 4x2300   |1 |
|**Standardowa_DS2** |2   |7   |14   |1000 |8000  |8 / 8x2300   |2 |
|**Standardowa_DS3** |4   |14  |28   |1000 |16000 |16 / 16x2300 |4 |
|**Standardowa_DS4** |8   |28  |56   |1000 |32000 |32 / 32x2300 |8 |

### <a name="dv2-series"></a>Seria Dv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standardowa_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |1 |
|**Standardowa_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standardowa_D3_v2** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standardowa_D4_v2** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 |
|**Standardowa_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>Seria DSv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standardowa_DS1_v2** |1   |3,5 |7   |1000 |4000  |4 / 4x2300   |1 |
|**Standardowa_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standardowa_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standardowa_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standardowa_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |


## <a name="compute-optimized"></a>Optymalizacja pod kątem obliczeń
### <a name="f-series"></a>Seria F
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej*

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standardowa_F1**  |1   |2   |16   |500 |3000  |4 / 4 x 500   |2 |
|**Standardowa_F2**  |2   |4   |32   |500 |6000  |8 / 8 x 500   |2 |
|**Standardowa_F4**  |4   |8   |64   |500 |12000 |16 / 16 x 500 |4 |
|**Standardowa_F8**  |8   |16  |128  |500 |24000 |32 / 32 x 500 |8 |
|**Standardowa_F16** |16  |32  |256  |500 |48000 |64 / 64x500 |8 |


### <a name="fs-series"></a>Seria Fs
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej*  

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standardowa_F1s**  |1   |2   |4   |1000 |4000  |4 / 4x2300   |2 |
|**Standardowa_F2s**  |2   |4   |8   |1000 |8000  |8 / 8x2300   |2 |
|**Standardowa_F4s**  |4   |8   |16  |1000 |16000 |16 / 16x2300 |4 |
|**Standardowa_F8s**  |8   |16  |32  |1000 |32000 |32 / 32x2300 |8 |
|**Standardowa_F16s** |16  |32  |64  |1000 |64000 |64 / 64x2300 |8 |


### <a name="fsv2-series"></a>Seria Fsv2
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej* 

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1000 |4000   |4 / 4x2300    |2 |
|**Standard_F4s_v2**  |4   |8   |32   |1000 |8000   |8 / 8x2300    |4 |
|**Standard_F8s_v2**  |8   |16  |64   |1000 |16000  |16 / 16x2300  |8 |
|**Standard_F16s_v2** |16  |32  |128  |1000 |32000  |32 / 32x2300  |8 |
|**Standard_F32s_v2** |32  |64  |256  |1000 |64000  |32 / 32x2300  |8 |
|**Standard_F64s_v2** |64  |128 |512  |1000 |128000 |32 / 32x2300  |8 |


## <a name="memory-optimized"></a>Optymalizacja pod kątem pamięci

Pamięć zoptymalizowanych rozmiarów maszyn wirtualnych zapewnia wysoką współczynnika pamięci do procesorów, który jest przeznaczony dla serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.

### <a name="mo-d"></a>Seria D
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|------------------|---|----|----|--------|------|------------|---------|
|**Standardowa_D11**  |2  |14  |100 |500     |6000  |8 / 8 x 500   |2 |
|**Standardowa_D12**  |4  |28  |200 |500     |12000 |16 / 16 x 500 |4 |
|**Standardowa_D13**  |8  |56  |400 |500     |24000 |32 / 32 x 500 |8 |
|**Standardowa_D14**  |16 |112 |800 |500     |48000 |64 / 64x500 |8 |

### <a name="mo-ds"></a>Seria DS
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standardowa_DS11**  |2  |14  |28  |1000    |8000  |8 / 8x2300   |2 |
|**Standardowa_DS12**  |4  |28  |56  |1000    |12000 |16 / 16x2300 |4 |
|**Standardowa_DS13**  |8  |56  |112 |1000    |32000 |32 / 32x2300 |8 |
|**Standardowa_DS14**  |16 |112 |224 |1000    |64000 |64 / 64x2300 |8 |

### <a name="mo-dv2"></a>Seria Dv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standardowa_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 |
|**Standardowa_D12_v2** |4   |28  |200  |500 |12000  |16 / 16 x 500  |4 |
|**Standardowa_D13_v2** |8   |56  |400  |500 |24000  |32 / 32 x 500  |8 |
|**Standardowa_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="mo-dsv2"></a>Seria DSv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standardowa_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standardowa_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standardowa_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standardowa_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |


## <a name="next-steps"></a>Kolejne kroki

[Uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md)
