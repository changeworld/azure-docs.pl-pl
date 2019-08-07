---
title: Metryki dla Azure NetApp Files | Microsoft Docs
description: Opisuje metryki dla Azure NetApp Files.
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
ms.date: 08/06/2019
ms.author: b-juche
ms.openlocfilehash: 9a56fb27fdf9e196291942041d68b249d7f16648
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839238"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Azure NetApp Files dostarcza metryki przydziału magazynu, rzeczywistego użycia magazynu, przepływności woluminu, operacji we/wy na sekundę oraz opóźnienia. Analizując te metryki, można lepiej zrozumieć wzorzec użycia i wydajność woluminu dla kont NetApp.  

## <a name="capacity_pools"></a>Metryki użycia dla pul pojemności

<!-- 
- *Volume pool allocated size*  
    This is the size (GiB) of the provisioned capacity pool.  
--> 
- *Użyte przydzieloną pulę woluminów*  
    Jest to całkowita liczba przydziałów woluminu (GiB) w danej puli pojemności (czyli całkowita wielkość przyznanych woluminów w puli pojemności). Jest to rozmiar wybrany podczas tworzenia woluminu.  
- *Łączny rozmiar logiczny puli woluminów*  
    Jest to całkowita ilość przestrzeni logicznej (GiB) używana między woluminami w puli pojemności.  
<!-- 
- *Volume pool total snapshot size*  
    This is the total of incremental logical space used by the snapshots.  
-->

## <a name="volumes"></a>Metryki użycia dla woluminów

<!-- 
- *Volume allocated size*   
    This is the volume size (quota) provisioned in GiB.  
--> 
- *Rozmiar logiczny woluminu*   
    Jest to całkowite miejsce logiczne używane w woluminie (GiB). Ten rozmiar obejmuje przestrzeni logicznej używanej przez aktywne systemy plików i migawki.  
- *Rozmiar migawki woluminu*   
    Jest to przyrostowe miejsce logiczne używane przez migawki w woluminie.  

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
