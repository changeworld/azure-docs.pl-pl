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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848786"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Azure NetApp Files dostarcza metryki przydziału magazynu, rzeczywistego użycia magazynu, przepływności woluminu, operacji we/wy na sekundę oraz opóźnienia. Analizując te metryki, można lepiej zrozumieć wzorzec użycia i wydajność woluminu dla kont NetApp.  

## <a name="capacity_pools"></a>Metryki użycia dla pul pojemności

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Użyte przydzieloną pulę woluminów*  
    Łączny limit przydziału woluminu (GiB) w danej puli pojemności (czyli łączny rozmiar zainicjowanych woluminów w puli pojemności)  
    Jest to rozmiar wybrany podczas tworzenia woluminu.  
- *Łączny rozmiar logiczny puli woluminów*  
    Całkowita ilość przestrzeni logicznej (GiB) używana między woluminami w puli pojemności  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Metryki użycia dla woluminów

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Rozmiar logiczny woluminu*   
    Całkowite miejsce logiczne używane w woluminie (GiB)  
    Ten rozmiar obejmuje przestrzeni logicznej używanej przez aktywne systemy plików i migawki.  
- *Rozmiar migawki woluminu*   
   Przyrostowe miejsce logiczne używane przez migawki w woluminie  

## <a name="performance-metrics-for-volumes"></a>Metryki wydajności dla woluminów

- *AverageReadLatency*   
    Średni czas odczytu z woluminu w milisekundach
- *AverageWriteLatency*   
    Średni czas zapisu z woluminu w milisekundach
- *ReadIops*   
    Liczba odczytów do woluminu na sekundę
- *WriteIops*   
    Liczba zapisów w woluminie na sekundę

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
