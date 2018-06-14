---
title: Obliczyć wyniki testów porównawczych dla maszyn wirtualnych systemu Windows Azure | Dokumentacja firmy Microsoft
description: Porównaj wyniki testu porównawczego obliczeń SPECint dla maszyn wirtualnych Azure systemem Windows Server.
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
ms.openlocfilehash: 2831f177bbcd02be02124812de144bd7a6867aa4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424704"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Obliczenia bazy danych wyników testów porównawczych dla maszyn wirtualnych systemu Windows
Następujące wyniki testu porównawczego SPECInt Pokaż wydajności obliczeniowej dla zestawienia maszyny Wirtualnej platformy Azure wysokiej wydajności z systemem Windows Server. Wyniki testu porównawczego obliczeniowe są także dostępne dla [maszyn wirtualnych systemu Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE]
> Numery Linux była niedawno aktualizowana i zawierają bardziej wyczerpujący zestaw maszyn wirtualnych.

## <a name="a-series---compute-intensive"></a>A-series obliczeniowych
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Średnia szybkość podstawowej | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Procesor Intel Xeon E5 procesora CPU — 2670 0 @ 2.6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Procesor Intel Xeon E5 procesora CPU — 2670 0 @ 2.6 GHz |10 |450.3 |7.0 |
| Standardowa_A10 |8 |1 |Procesor Intel Xeon E5 procesora CPU — 2670 0 @ 2.6 GHz |5 |235.6 |0.9 |
| Standardowa_A11 |16 |2 |Procesor Intel Xeon E5 procesora CPU — 2670 0 @ 2.6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Seria Dv2
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Średnia szybkość podstawowej | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_D1_v2 |1 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |83 |36.6 |2.6 |
| Standardowa_D2_v2 |2 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |27 |70.0 |3.7 |
| Standardowa_D3_v2 |4 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |130.5 |4.4 |
| Standardowa_D4_v2 |8 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |238.1 |5.2 |
| Standardowa_D5_v2 |16 |2 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |14 |460.9 |15.4 |
| Standardowa_D11_v2 |2 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |70.1 |3.7 |
| Standardowa_D12_v2 |4 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |2 |132.0 |1.4 |
| Standardowa_D13_v2 |8 |1 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |17 |235.8 |3.8 |
| Standardowa_D14_v2 |16 |2 |Procesor Intel Xeon E5-2673 v3 @ 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G serii, serie GS
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Średnia szybkość podstawowej | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Procesor Intel Xeon E5-2698B v3 @ 2 GHz |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Procesor Intel Xeon E5-2698B v3 @ 2 GHz |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Procesor Intel Xeon E5-2698B v3 @ 2 GHz |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Procesor Intel Xeon E5-2698B v3 @ 2 GHz |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Procesor Intel Xeon E5-2698B v3 @ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>Seria H
| Rozmiar | Procesory wirtualne vCPU | Węzły NUMA | Procesor CPU | Uruchomienia | Średnia szybkość podstawowej  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_H8 |8 |1 |Procesor Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |297.4 |0.9 |
| Standardowa_H16 |16 |2 |Procesor Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |575.8 |6.8 |
| Standardowa_H8m |8 |1 |Procesor Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |297.0 |1.2 |
| Standardowa_H16m |16 |2 |Procesor Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |572.2 |3.9 |
| Warstwie standardowa_h16r |16 |2 |Procesor Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |573.2 |2.9 |
| Warstwie standardowa_h16mr |16 |2 |Procesor Intel Xeon E5-2667 v3 @ 3,2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>O SPECint
Numery systemu Windows zostały obliczone, uruchamiając [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) w systemie Windows Server. SPECint był uruchamiany z jednej kopii na vCPU przy użyciu opcji szybkości podstawowej (SPECint_rate2006). SPECint składa się z 12 testy w oddzielnych, każdy trzykrotne uruchomienie, biorąc wartość mediany każdego testu i wagi je do utworzenia złożonego wynik. Te testy zostały uruchomione następnie między wieloma maszynami wirtualnymi, aby zapewnić średnie wyniki wyświetlane.

## <a name="next-steps"></a>Kolejne kroki
* Pojemność, dysku szczegóły i dodatkowe uwagi dotyczące wybierania rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

