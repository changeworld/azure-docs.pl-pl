---
title: Wyniki testów porównawczych wystąpień obliczeniowych dla maszyn wirtualnych Windows Azure | Dokumentacja firmy Microsoft
description: Porównaj wyniki testów porównawczych obliczeń SPECint dla maszyn wirtualnych platformy Azure z systemem Windows Server.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 43d0ab6552847df7f1f2a8599dcc7cb9a8fcb57b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844078"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Wyniki testów porównawczych wystąpień obliczeniowych dla maszyn wirtualnych Windows
Następujące wyniki testów porównawczych SPECInt Pokaż wydajności obliczeniowej dla zestawienia maszyny Wirtualnej platformy Azure o wysokiej wydajności z systemem Windows Server. Wyniki testów porównawczych obliczeniowych są również dostępne dla [maszyn wirtualnych systemu Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Numery systemu Linux został ostatnio zaktualizowany i zawiera bardziej rozbudowany zestaw maszyn wirtualnych.

## <a name="a-series---compute-intensive"></a>Seria A — intensywnych obliczeń
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Podstawowa stawka średni | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Procesor Intel Xeon Procesora E5-2670 0 \@ 2,6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Procesor Intel Xeon Procesora E5-2670 0 \@ 2,6 GHz |10 |450.3 |7.0 |
| Standardowa_A10 |8 |1 |Procesor Intel Xeon Procesora E5-2670 0 \@ 2,6 GHz |5 |235.6 |0.9 |
| Standardowa_A11 |16 |2 |Procesor Intel Xeon Procesora E5-2670 0 \@ 2,6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Seria Dv2
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Podstawowa stawka średni | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_D1_v2 |1 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |83 |36.6 |2.6 |
| Standardowa_D2_v2 |2 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |27 |70.0 |3.7 |
| Standardowa_D3_v2 |4 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |19 |130.5 |4.4 |
| Standardowa_D4_v2 |8 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |19 |238.1 |5.2 |
| Standardowa_D5_v2 |16 |2 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |14 |460.9 |15.4 |
| Standardowa_D11_v2 |2 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |19 |70.1 |3.7 |
| Standardowa_D12_v2 |4 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |2 |132.0 |1.4 |
| Standardowa_D13_v2 |8 |1 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |17 |235.8 |3.8 |
| Standardowa_D14_v2 |16 |2 |Procesor Intel Xeon E5-2673 v3 \@ 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>Seria G, seria GS
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Podstawowa stawka średni | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Maszyna wirtualna Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |31 |71.8 |6.5 |
| Maszyna wirtualna Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |5 |133.4 |13.0 |
| Maszyna wirtualna Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |6 |242.3 |6.0 |
| Maszyna wirtualna Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |15 |398.9 |6.0 |
| Maszyna wirtualna Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 \@ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>Seria H
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Podstawowa stawka średni  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_H8 |8 |1 |Procesor Intel Xeon E5-2667 v3 \@ 3,2 GHz |5 |297.4 |0.9 |
| Standardowa_H16 |16 |2 |Procesor Intel Xeon E5-2667 v3 \@ 3,2 GHz |5 |575.8 |6.8 |
| Standardowa_H8m |8 |1 |Procesor Intel Xeon E5-2667 v3 \@ 3,2 GHz |5 |297.0 |1.2 |
| Standardowa_H16m |16 |2 |Procesor Intel Xeon E5-2667 v3 \@ 3,2 GHz |5 |572.2 |3.9 |
| Maszyna wirtualna standard_h16r — |16 |2 |Procesor Intel Xeon E5-2667 v3 \@ 3,2 GHz |5 |573.2 |2.9 |
| Maszyna wirtualna standard_h16mr — |16 |2 |Procesor Intel Xeon E5-2667 v3 \@ 3,2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>Temat SPECint
Numery Windows zostały obliczone przez uruchomienie [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) w systemie Windows Server. SPECint została uruchomiona przy użyciu opcji stawkę za podstawowy (SPECint_rate2006) przy użyciu jednej kopii na procesor wirtualny vCPU. SPECint składa się z 12 oddzielnych testów, każdy trzykrotne uruchomienie, biorąc wartość mediany z każdego testu i wagi je w celu utworzenia złożonego wynik. Następnie uruchomienia tych testów na wielu maszynach wirtualnych w celu zapewnienia średnie wyniki wyświetlane.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać wielkości magazynu, szczegóły dysku i dodatkowe zagadnienia dotyczące wybierania rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

