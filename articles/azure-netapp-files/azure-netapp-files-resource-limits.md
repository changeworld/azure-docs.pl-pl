---
title: Limity zasobów dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano limity dla zasobów usługi Azure Files NetApp, w tym limity dla pul pojemności, woluminów i delegowanego podsieci.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057015"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure Files NetApp
Opis limity zasobów dla usługi Azure Files NetApp ułatwia zarządzanie woluminami.

## <a name="capacity_pools"></a>Pule pojemności

- Minimalny rozmiar pojedynczej puli pojemności wynosi 4 TiB, a maksymalny rozmiar wynosi 500 TiB. 
- Każda pula pojemności może należeć tylko do jednego konta usługi NetApp. Można jednak mieć wiele pul pojemności w ramach konta usługi NetApp.  

## <a name="volumes"></a>Woluminy

- Minimalny rozmiar jednego woluminu wynosi 100 GiB, a maksymalny rozmiar wynosi 92 TiB.
- Można mieć maksymalnie 100 woluminów na subskrypcję platformy Azure na region.  

## <a name="delegated_subnet"></a>Delegowane podsieci 

W każdym Azure Virtual Network (Vnet) można przekazać tylko jednej podsieci do usługi Azure Files NetApp.

## <a name="next-steps"></a>Kolejne kroki

[Zrozumienie hierarchii magazynu usługi Azure Files NetApp](azure-netapp-files-understand-storage-hierarchy.md)
