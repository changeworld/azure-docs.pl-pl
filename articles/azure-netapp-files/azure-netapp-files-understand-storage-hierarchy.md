---
title: Omówienie hierarchii magazynu usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano hierarchię magazynu usługi Azure NetApp Files, obejmującą konta, pule pojemności i woluminy.
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
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: 1cce1883295277f6c6c36d686d90370238265dbf
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775853"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Omówienie hierarchii magazynu usługi Azure NetApp Files

Przed utworzeniem woluminu w usłudze Azure NetApp Files należy kupić i skonfigurować pulę aprowizowanej pojemności.  Do skonfigurowania puli pojemności potrzebne jest konto usługi NetApp. Zrozumienie hierarchii magazynu pomaga w konfigurowaniu zasobów usługi Azure NetApp Files i zarządzaniu nimi.

## <a name="azure_netapp_files_account"></a>Konta usługi NetApp

- Konto usługi NetApp grupuje składowe pule pojemności do celów administracyjnych.  
- Konto usługi NetApp różni się od ogólnego konta usługi Azure Storage. 
- Konto usługi NetApp ma zasięg regionalny.   
- W jednym regionie można mieć wiele kont usługi NetApp, ale każde konto usługi NetApp jest powiązane tylko z jednym regionem.

## <a name="capacity_pools"></a>Pule pojemności

- Pule pojemności są mierzone według aprowizowanej pojemności.  
- Pojemność jest aprowizowana zgodnie z liczbą zakupionych stałych jednostek SKU (zawierających na przykład 4 TiB pojemności).
- Minimalny rozmiar pojedynczej puli pojemności wynosi 4 TiB, a maksymalny rozmiar wynosi 500 TiB. 
- Pula pojemności może mieć tylko jeden poziom usługi.  
  Obecnie jest dostępny tylko poziom usługi Premium.
- Każda pula pojemności może należeć tylko do jednego konta usługi NetApp. Można jednak mieć wiele pul pojemności w ramach konta usługi NetApp.  
- Nie można przenosić pul pojemności między kontami usługi NetApp.   
  Na przykład na poniższym [diagramie koncepcyjnym hierarchii magazynu](#conceptual_diagram_of_storage_hierarchy) nie można przenieść puli pojemności 1 z konta usługi NetApp w regionie Wschodnie stany USA na konto usługi NetApp w regionie Zachodnie stany USA 2.  

## <a name="volumes"></a>Woluminy

- Woluminy są mierzone według użycia pojemności logicznej i można je skalować. Minimalny rozmiar jednego woluminu wynosi 100 GiB, a maksymalny rozmiar wynosi 92 TiB.
- Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.
-   Można mieć maksymalnie 100 woluminów na subskrypcję platformy Azure na region. 
- Każdy wolumin należy tylko do jednej puli, ale jedna pula może zawierać wiele woluminów. 
- Można przenosić woluminy między pulami w ramach jednego konta usługi NetApp.    
  Na przykład na poniższym [diagramie koncepcyjnym hierarchii magazynu](#conceptual_diagram_of_storage_hierarchy) można by przenieść woluminy z puli pojemności 1 do puli pojemności 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagram koncepcyjny hierarchii magazynu 
Poniższy przykład przedstawia zależności między subskrypcją platformy Azure, kontami usługi NetApp, pulami pojemności i woluminami.   

![Diagram koncepcyjny hierarchii magazynu](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Kolejne kroki

- [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Rejestrowanie w usłudze Azure NetApp Files](azure-netapp-files-register.md)
