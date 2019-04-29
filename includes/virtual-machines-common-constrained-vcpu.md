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
ms.openlocfilehash: 82cbffb257d85197848b8bca14231e5363d6d45c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729850"
---
Niektórych obciążeń bazy danych, takich jak SQL Server lub Oracle wymagają dużą ilość pamięci, magazynu i przepustowości we/wy, ale nie liczba rdzeni wysokiej. Wiele różnych obciążeń bazy danych nie są intensywnie korzystających z procesora CPU. Platforma Azure oferuje określonych rozmiarów maszyn wirtualnych, których można ograniczyć liczbę procesorów wirtualnych vCPU maszyny Wirtualnej, aby zmniejszyć koszty licencjonowania oprogramowania, przy zachowaniu tej samej pamięci, magazynu i przepustowości we/wy.

Liczba procesorów wirtualnych, które mogą być ograniczone do połowy lub jeden kwartału od oryginalnego rozmiaru maszyny Wirtualnej. Te nowe rozmiary maszyn wirtualnych mają sufiks, który określa liczbę aktywnych wirtualnych procesorów CPU, aby ułatwić ich identyfikację.

Na przykład, bieżący rozmiar maszyny Wirtualnej Standard_GS5 dołączono 32 procesorów wirtualnych Vcpu, 448 GB pamięci RAM, 64 dyski (do 256 TB) i 80 000 operacji We/Wy lub 2 GB/s przepustowości we/wy. Nowe maszyny Wirtualne o rozmiarach Standard_GS5-16 i Standard_GS5 8 jest dostarczany z 16 i 8 procesorów wirtualnych active odpowiednio przy zachowaniu pozostałych specyfikacje Standard_GS5 pamięci, magazynu i przepustowości we/wy.

Licencjonowania opłaty dla programu SQL Server lub Oracle są ograniczone do nowego liczbę procesorów wirtualnych vCPU i innych produktów należy opłatami na podstawie nowego liczbę procesorów wirtualnych vCPU. Skutkuje to 50-75% wzrost stosunek specyfikacje maszyny Wirtualnej do aktywnego Vcpu (rozliczana). Te nowe rozmiary maszyn wirtualnych, które są dostępne tylko na platformie Azure, umożliwiają obciążeniom większe użycie procesora za ułamek kosztu licencjonowania na rdzeń. W tej chwili koszt obliczeń, w tym Licencjonowanie systemu operacyjnego, pozostaje taka sama, jak jako oryginalnego rozmiaru. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych platformy Azure dla więcej różnych obciążeń bazy danych ekonomiczne](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name (Nazwa)                | Procesor wirtualny | Specyfikacja           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Takie same jak M8ms    |
| Standard_M8-4ms     | 4    | Takie same jak M8ms    |
| Standard_M16-4ms    | 4    | Takie same jak M16ms   |
| Standard_M16-8ms    | 8    | Takie same jak M16ms   |
| Standard_M32-8ms    | 8    | Takie same jak M32ms   |
| Standard_M32-16ms   | 16   | Takie same jak M32ms   |
| Standard_M64-32ms   | 32   | Takie same jak M64ms   |
| Standard_M64-16ms   | 16   | Takie same jak M64ms   |
| Standard_M128-64ms  | 64   | Takie same jak M128ms  |
| Standard_M128-32ms  | 32   | Takie same jak M128ms  |
| Standard_E4-2s_v3   | 2    | Takie same jak E4s_v3  |
| Standard_E8-4s_v3   | 4    | Takie same jak E8s_v3  |
| Standard_E8-2s_v3   | 2    | Takie same jak E8s_v3  |
| Standard_E16-8s_v3  | 8    | Takie same jak E16s_v3 |
| Standard_E16-4s_v3  | 4    | Takie same jak E16s_v3 |
| Standard_E32 16_v3  | 16   | Takie same jak E32s_v3 |
| Standard_E32-8s_v3  | 8    | Takie same jak E32s_v3 |
| Standard_E64-32s_v3 | 32   | Takie same jak E64s_v3 |
| Standard_E64-16s_v3 | 16   | Takie same jak E64s_v3 |
| Standard_GS4-8      | 8    | Takie same jak GS4     |
| Standard_GS4 4      | 4    | Takie same jak GS4     |
| Standard_GS5-16     | 16   | Takie same jak GS5     |
| Standard_GS5-8      | 8    | Takie same jak GS5     |
| Standard_DS11-1_v2  | 1    | Takie same jak DS11_v2 |
| Standard_DS12-2_v2  | 2    | Takie same jak DS12_v2 |
| Standard_DS12-1_v2  | 1    | Takie same jak DS12_v2 |
| Standard_DS13-4_v2  | 4    | Takie same jak DS13_v2 |
| Standard_DS13-2_v2  | 2    | Takie same jak DS13_v2 |
| Standard_DS14-8_v2  | 8    | Takie same jak DS14_v2 |
| Standard_DS14-4_v2  | 4    | Takie same jak DS14_v2 |
