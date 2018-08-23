---
title: Rozmiary maszyn wirtualnych obsługiwanych w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dokumentacja obsługiwane rozmiary maszyn wirtualnych w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: mabrigg
ms.openlocfilehash: 74d521f1890f8ff6567802c1a16f4169dbf4a53f
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054388"
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Rozmiary maszyn wirtualnych obsługiwanych w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

W tym artykule wymieniono rozmiarów maszyn wirtualnych (VM), które są dostępne w usłudze Azure Stack.

## <a name="general-purpose"></a>Zastosowania ogólne

Rozmiary maszyn wirtualnych ogólnego przeznaczenia udostępniają o zrównoważonym obciążeniu współczynnik procesora CPU w stosunku do pamięci. Są one używane do testowania i programowania, małych i średnich baz danych, oraz o niewielkim i średnim ruchu, serwery sieci web.

### <a name="basic-a"></a>Podstawowa A
|Rozmiar — rozmiar\nazwa |Procesor wirtualny     |Memory (Pamięć) | Maksymalny rozmiar dysku tymczasowego | System operacyjny maksymalna przepływność dysków: (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna przepływność dysków danych (operacje We/Wy) | Maksymalna liczba kart sieciowych |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1x300  |1   |
|**A1\Basic_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2 x 300  |1   |
|**A2\Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4x300  |1   |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8x300  |1   |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16 X 300 |1   |

### <a name="standard-a"></a>A standardowe 
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |    
|----------------|--|------|----|----|----|-------|---------|
|**Standardowa_A0** |1 |0,768 |20  |500 |500 |1 x 500  |1 / 100  |
|**Standard_A1** |1 |1,75  |70  |500 |500 |2 x 500  |1 / 500  |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4 x 500  |1 / 500  |
|**Standard_A3** |4 |7     |285 |500 |500 |8 x 500  |2 / 1000 |
|**Standard_A4** |8 |14    |605 |500 |500 |16 x 500 |4 / 2000 |
|**Standard_A5** |2 |14    |135 |500 |500 |4 x 500  |2 / 500  |
|**Standard_A6** |4 |28    |285 |500 |500 |8 x 500  |2 / 1000 |
|**Standard_A7** |8 |56    |605 |500 |500 |16 x 500 |4 / 2000 |

### <a name="av2-series"></a>Seria Av2
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej*

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1000  |2 / 2 x 500   |2 / 250  |
|**Maszyna wirtualna Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4 x 500   |2 / 500  |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8 x 500   |4 / 1000 |
|**Maszyna wirtualna Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16 x 500 |8 / 2000 |
|**Maszyna wirtualna Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4 x 500   |2 / 500  |
|**Maszyna wirtualna Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8 x 500   |4 / 1000 |
|**Maszyna wirtualna Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16 x 500 |8 / 2000 |

### <a name="d-series"></a>Seria D
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|----------------|----|----|-----|----|------|------------|---------|
|**Maszyna wirtualna Standard_D1** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |1 / 500  |
|**Maszyna wirtualna Standard_D2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 / 1000 |
|**Maszyna wirtualna Standard_D3** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 / 2000 |
|**Maszyna wirtualna Standard_D4** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 / 4000 |


### <a name="ds-series"></a>Seria DS
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3,5 |7    |1000 |4000  |4 / 4x2300   |1 / 500  |
|**Standard_DS2** |2   |7   |14   |1000 |8000  |8 / 8x2300   |2 / 1000 |
|**Standard_DS3** |4   |14  |28   |1000 |16000 |16 / 16x2300 |4 / 2000 |
|**Standard_DS4** |8   |28  |56   |1000 |32000 |32 / 32x2300 |8 / 4000 |

### <a name="dv2-series"></a>Seria Dv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-------------------|----|----|-----|----|------|------------|---------|
|**Maszyna wirtualna standard_d1_v2 —** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |1 / 500  |
|**Maszyna wirtualna Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 / 1000 |
|**Maszyna wirtualna Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 / 2000 |
|**Maszyna wirtualna Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 / 4000 |
|**Maszyna wirtualna Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 / 8000 |

### <a name="dsv2-series"></a>Seria DSv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standardowa_ds1_v2** |1   |3,5 |7   |1000 |4000  |4 / 4x2300   |1 / 750  |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 / 1500 |
|**Standardowa_ds3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 / 3000 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 / 6000 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 / 10000 |


## <a name="compute-optimized"></a>Optymalizacja pod kątem obliczeń
### <a name="f-series"></a>Seria F
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej*

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-----------------|----|----|-----|----|------|------------|---------|
|**Maszyna wirtualna Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4 x 500   |2 / 750  |
|**Maszyna wirtualna Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8 x 500   |2 / 1500 |
|**Maszyna wirtualna Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16 x 500 |4 / 3000 |
|**Maszyna wirtualna standard_f8 —**  |8   |16  |128  |500 |24000 |32 / 32 x 500 |8 / 6000 |
|**Maszyna wirtualna standard_f16 —** |16  |32  |256  |500 |48000 |64 / 64x500 |8 / 6000 - 12000  |


### <a name="fs-series"></a>Seria Fs
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej*  

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1000 |4000  |4 / 4x2300   |2 / 750  |
|**Standard_F2s**  |2   |4   |8   |1000 |8000  |8 / 8x2300   |2 / 1500 |
|**Standard_F4s**  |4   |8   |16  |1000 |16000 |16 / 16x2300 |4 / 3000 |
|**Standard_F8s**  |8   |16  |32  |1000 |32000 |32 / 32x2300 |8 / 6000 |
|**Standard_F16s** |16  |32  |64  |1000 |64000 |64 / 64x2300 |8 / 6000 - 12000  |


### <a name="fsv2-series"></a>Seria Fsv2
*Wymaga usługi Azure Stack w wersji 1804 lub nowszej* 

|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1000 |4000   |4 / 4x2300    |Średnia |
|**Standard_F4s_v2**  |4   |8   |32   |1000 |8000   |8 / 8x2300    |Średnia |
|**Standard_F8s_v2**  |8   |16  |64   |1000 |16000  |16 / 16x2300  |Wysoka     |
|**Standard_F16s_v2** |16  |32  |128  |1000 |32000  |32 / 32x2300  |Wysoka     |
|**Standard_F32s_v2** |32  |64  |256  |1000 |64000  |32 / 32x2300  |Wysoka  |
|**Standard_F64s_v2** |64  |128 |512  |1000 |128000 |32 / 32x2300  |Bardzo wysoka  |


## <a name="memory-optimized"></a>Optymalizacja pod kątem pamięci

Pamięć zoptymalizowanych rozmiarów maszyn wirtualnych zapewnia wysoką współczynnika pamięci do procesorów, który jest przeznaczony dla serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.

### <a name="mo-d"></a>Seria D
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|------------------|---|----|----|--------|------|------------|---------|
|**Maszyna wirtualna Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8 x 500   |2 / 1000 |
|**Maszyna wirtualna Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16 x 500 |4 / 2000 |
|**Maszyna wirtualna Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32 x 500 |8 / 4000 |
|**Maszyna wirtualna Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64x500 |8 / 8000 |

### <a name="mo-ds"></a>Seria DS
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1000    |8000  |8 / 8x2300   |2 / 1000 |
|**Standard_DS12**  |4  |28  |56  |1000    |12000 |16 / 16x2300 |4 / 2000 |
|**Standard_DS13**  |8  |56  |112 |1000    |32000 |32 / 32x2300 |8 / 4000 |
|**Standard_DS14**  |16 |112 |224 |1000    |64000 |64 / 64x2300 |8 / 8000 |

### <a name="mo-dv2"></a>Seria Dv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Maszyna wirtualna Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 / 1500 |
|**Maszyna wirtualna Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16 x 500  |4 / 3000 |
|**Maszyna wirtualna Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32 x 500  |8 / 6000 |
|**Maszyna wirtualna Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 / 10000 |


### <a name="mo-dsv2"></a>Seria DSv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazynu tymczasowego (GiB)  | System operacyjny maksymalna przepływność dysku (IOPS) | Maksymalna przepływność magazynu tymczasowego (IOPS) | Maksymalna liczba dysków danych / przepływność (IOPS) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |8 / 8x2300    |2 / 1500 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |16 / 16x2300  |4 / 3000 |
|**Standardowa_ds13_v2** |8   |56  |112  |1000 |32000  |32 / 32x2300  |8 / 6000 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |64 / 64x2300  |8 / 10000 |


## <a name="next-steps"></a>Kolejne kroki

[Uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md)
