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
ms.openlocfilehash: eaf490979167ea5cbfe5846e62444526310452f8
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269548"
---
Niektórych obciążeń bazy danych, takich jak SQL Server lub Oracle wymagają pamięci wysokiej, magazynu i przepustowości we/wy, ale nie liczba rdzeni wysoki. Wiele obciążeń bazy danych nie są użycie Procesora CPU. System Azure oferuje niektórych rozmiarów maszyn wirtualnych, w którym można ograniczyć liczbę vCPU maszyny Wirtualnej, aby zredukować koszty licencjonowania oprogramowania, przy zachowaniu tej samej pamięci, magazynu i przepustowości we/wy.

Liczba vCPU może być ograniczona do połowy lub co kwartał oryginalnego rozmiaru maszyny Wirtualnej. Te nowe rozmiary maszyny Wirtualnej mają sufiks, który określa liczbę aktywnych Vcpu, aby łatwiej zidentyfikować.

Na przykład, bieżący rozmiar maszyny Wirtualnej Standard_GS5 jest dostarczany z 32 Vcpu, 448 GB pamięci RAM, 64 dyski (do 256 TB) i 80 000 IOPs lub 2 GB/s przepustowości we/wy. Nowa maszyna wirtualna rozmiary Standard_GS5 16 i Standard_GS5 8 jest dostarczany z 16 i 8 active Vcpu, przy zachowaniu reszty specyfikacji Standard_GS5 pamięci, magazynu i przepustowości we/wy.

Licencjonowania opłaty dla programu SQL Server lub Oracle są ograniczone do liczby nowych vCPU i innych produktów należy naliczane na podstawie nowych vCPU liczby. Powoduje to 50% do 75% wzrost stosunek specyfikacji maszyny Wirtualnej do aktywnego Vcpu (rozliczeniowy). Te nowe rozmiary maszyn wirtualnych, które są dostępne tylko na platformie Azure, umożliwiają obciążeniom większe użycie procesora za ułamek kosztu licencjonowania na rdzeń. W tej chwili koszt obliczeń, w tym licencjonowania systemu operacyjnego, pozostaje ten sam jako oryginalny rozmiar. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych Azure więcej obciążeń ekonomicznego bazy danych](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name (Nazwa)                | Procesor wirtualny | Specyfikacja           |
|---------------------|------|-----------------|
| 2 Standard_M8 MS     | 2    | Identyczny M8ms    |
| Standard_M8 4ms     | 4    | Identyczny M8ms    |
| Standard_M16 4ms    | 4    | Identyczny M16ms   |
| Standard_M16 — zazwyczaj około 8 MS    | 8    | Identyczny M16ms   |
| Standard_M32 — zazwyczaj około 8 MS    | 8    | Identyczny M32ms   |
| 16 Standard_M32 MS   | 16   | Identyczny M32ms   |
| Standard_M64 32ms   | 32   | Identyczny M64ms   |
| 16 Standard_M64 MS   | 16   | Identyczny M64ms   |
| Standard_M128 64ms  | 64   | Identyczny M128ms  |
| Standard_M128 32ms  | 32   | Identyczny M128ms  |
| Standard_E4 2s_v3   | 2    | Identyczny E4s_v3  |
| Standard_E8 4s_v3   | 4    | Identyczny E8s_v3  |
| Standard_E8 2s_v3   | 2    | Identyczny E8s_v3  |
| Standard_E16 8s_v3  | 8    | Identyczny E16s_v3 |
| Standard_E16 4s_v3  | 4    | Identyczny E16s_v3 |
| Standard_E32 16_v3  | 16   | Identyczny E32s_v3 |
| Standard_E32 8s_v3  | 8    | Identyczny E32s_v3 |
| Standard_E64 32s_v3 | 32   | Identyczny E64s_v3 |
| Standard_E64 16s_v3 | 16   | Identyczny E64s_v3 |
| Standard_GS4-8      | 8    | Identyczny GS4     |
| Standard_GS4 4      | 4    | Identyczny GS4     |
| Standard_GS5-16     | 16   | Identyczny GS5     |
| Standard_GS5-8      | 8    | Identyczny GS5     |
| Standard_DS11 1_v2  | 1    | Identyczny DS11_v2 |
| Standard_DS12 2_v2  | 2    | Identyczny DS12_v2 |
| Standard_DS12 1_v2  | 1    | Identyczny DS12_v2 |
| Standard_DS13 4_v2  | 4    | Identyczny DS13_v2 |
| Standard_DS13 2_v2  | 2    | Identyczny DS13_v2 |
| Standard_DS14 8_v2  | 8    | Identyczny DS14_v2 |
| Standard_DS14 4_v2  | 4    | Identyczny DS14_v2 |
