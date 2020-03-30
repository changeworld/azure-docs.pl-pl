---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24fbab2bb637edf6c7e31215cdc7ac82bdb0ebab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183226"
---
Niektóre obciążenia bazy danych, takie jak SQL Server lub Oracle, wymagają dużej ilości pamięci, pamięci masowej i przepustowości we/wy, ale nie dużej liczby rdzeni. Wiele obciążeń bazy danych nie są intensywnie korzystające z procesora CPU. Platforma Azure oferuje pewne rozmiary maszyn wirtualnych, w których można ograniczyć liczbę procesorów wirtualnych maszyn wirtualnych, aby zmniejszyć koszty licencjonowania oprogramowania, zachowując tę samą przepustowość pamięci, magazynu i we/wy.

Liczba procesorów wirtualnych może być ograniczona do połowy lub jednej czwartej oryginalnego rozmiaru maszyny Wirtualnej. Te nowe rozmiary maszyn wirtualnych mają sufiks, który określa liczbę aktywnych procesorów wirtualnych, aby ułatwić ich identyfikację.

Na przykład bieżący rozmiar maszyny Wirtualnej Standard_GS5 jest wyposażony w 32 procesory wirtualne, 448 GB pamięci RAM, 64 dyski (do 256 TB) i 80 000 IOp lub 2 GB/s przepustowości we/wy. Nowe rozmiary maszyn wirtualnych Standard_GS5-16 i Standard_GS5-8 są wyposażone odpowiednio w 16 i 8 aktywnych procesorów wirtualnych, zachowując pozostałe specyfikacje Standard_GS5 dla przepustowości pamięci, pamięci masowej i we/wy.

Opłaty licencyjne pobierane za program SQL Server lub Oracle są ograniczone do nowej liczby procesorów wirtualnych, a inne produkty powinny być naliczane na podstawie nowej liczby procesorów wirtualnych. Powoduje to wzrost stosunku specyfikacji maszyny Wirtualnej o 50% do 75% do aktywnych (rozliczanych) procesorów wirtualnych. Te nowe rozmiary maszyn wirtualnych umożliwiają obciążeniom klientów korzystanie z tej samej pamięci, magazynu i przepustowości we/wy przy jednoczesnej optymalizacji kosztów licencjonowania oprogramowania. W tej chwili koszt obliczeń, który obejmuje licencjonowanie systemu operacyjnego, pozostaje taki sam jak rozmiar oryginalny. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych platformy Azure, aby uzyskać bardziej ekonomiczne obciążenia baz danych.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)


| Nazwa                | Procesor wirtualny | Specyfikacje           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Tak samo jak M8ms    |
| Standard_M8-4ms     | 4    | Tak samo jak M8ms    |
| Standard_M16-4ms    | 4    | Tak samo jak M16ms   |
| Standard_M16-8ms    | 8    | Tak samo jak M16ms   |
| Standard_M32-8ms    | 8    | Tak samo jak M32ms   |
| Standard_M32-16ms   | 16   | Tak samo jak M32ms   |
| Standard_M64-32ms   | 32   | Tak samo jak M64ms   |
| Standard_M64-16ms   | 16   | Tak samo jak M64ms   |
| Standard_M128-64ms  | 64   | Tak samo jak M128ms  |
| Standard_M128-32ms  | 32   | Tak samo jak M128ms  |
| Standard_E4-2s_v3   | 2    | Tak samo jak E4s_v3  |
| Standard_E8 4s_v3   | 4    | Tak samo jak E8s_v3  |
| Standard_E8 2s_v3   | 2    | Tak samo jak E8s_v3  |
| Standard_E16-8s_v3  | 8    | Tak samo jak E16s_v3 |
| Standard_E16 4s_v3  | 4    | Tak samo jak E16s_v3 |
| Standard_E32 16s_v3 | 16   | Tak samo jak E32s_v3 |
| Standard_E32 8s_v3  | 8    | Tak samo jak E32s_v3 |
| Standard_E64-32s_v3 | 32   | Tak samo jak E64s_v3 |
| Standard_E64 16s_v3 | 16   | Tak samo jak E64s_v3 |
| Standard_GS4-8      | 8    | Tak samo jak GS4     |
| Standard_GS4-4      | 4    | Tak samo jak GS4     |
| Standard_GS5-16     | 16   | Tak samo jak GS5     |
| Standardowa_GS5-8      | 8    | Tak samo jak GS5     |
| Standard_DS11 1_v2  | 1    | Tak samo jak DS11_v2 |
| Standard_DS12 2_v2  | 2    | Tak samo jak DS12_v2 |
| Standard_DS12 1_v2  | 1    | Tak samo jak DS12_v2 |
| Standard_DS13 4_v2  | 4    | Tak samo jak DS13_v2 |
| Standard_DS13 2_v2  | 2    | Tak samo jak DS13_v2 |
| Standard_DS14-8_v2  | 8    | Tak samo jak DS14_v2 |
| Standard_DS14 4_v2  | 4    | Tak samo jak DS14_v2 |
