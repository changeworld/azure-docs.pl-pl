---
title: Metryki dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano metryki dla plików NetApp platformy Azure.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460436"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Usługa Azure NetApp Files udostępnia metryki dotyczące przydzielonego magazynu, rzeczywistego użycia magazynu, liczby we/wy i opóźnienia. Analizując te dane, możesz lepiej zrozumieć wzorzec użycia i wydajność wolumenu kont NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metryki użycia dla puli pojemności

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pula przydzielona do rozmiaru woluminu*  
    Całkowity przydział woluminu (GiB) w danej puli pojemności (czyli suma rozmiarów aprowizacji woluminów w puli pojemności).  
    Ten rozmiar jest rozmiarem wybranym podczas tworzenia woluminu.  
- *Rozmiar zużytego basenu*  
    Całkowita ilość miejsca logicznego (GiB) używanego w woluminach w puli pojemności.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metryki użycia woluminów

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Rozmiar zużytej objętości*   
    Całkowita przestrzeń logiczna używana w woluminie (GiB).  
    Ten rozmiar obejmuje przestrzeń logiczną używaną przez aktywne systemy plików i migawki.  
- *Rozmiar migawki woluminu*   
   Przyrostowa przestrzeń logiczna używana przez migawki w woluminie.  

## <a name="performance-metrics-for-volumes"></a>Metryki wydajności dla woluminów

- *ŚredniareadLatency*   
    Średni czas odczytów z woluminu w milisekundach.
- *Średnia Średniawłasność*   
    Średni czas zapisu z woluminu w milisekundach.
- *ReadIops (ReadIops)*   
    Liczba odczytów do woluminu na sekundę.
- *WriteIops (WriteIops)*   
    Liczba zapisów do woluminu na sekundę.

## <a name="next-steps"></a>Następne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
