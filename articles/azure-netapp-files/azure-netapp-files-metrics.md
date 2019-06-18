---
title: Metryki dla usługi pliki Azure NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano metryki dla usługi Azure Files NetApp.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61084943"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Usługa Azure Files NetApp generuje dane pomiarowe na przydzielenia pamięci, użycie rzeczywisty magazyn, przepływność woluminu, operacje We/Wy i opóźnienia. Analizując te metryki, możesz uzyskać lepsze zrozumienie wydajności wzorzec i wolumin użycia konta NetApp.  

## <a name="capacity_pools"></a>Metryki użycia pojemności pul

- *Rozmiar puli przydzielona woluminu*  
    Jest to rozmiar (GiB) puli zaprowizowaną pojemnością.  
- *Pula woluminów przydzielonych używane*  
    Jest to łączny limit przydziału woluminu (GiB) w danej pojemności puli (oznacza to, łączny rozmiar aprowizowanego woluminy w puli pojemności). Jest to rozmiar, który został wybrany podczas tworzenia woluminu.  
- *Całkowity rozmiar logiczny woluminu puli*  
    Jest to łączna liczba logicznych miejsca (GiB) używane w całym woluminy w puli pojemności.  
- *Rozmiar migawki całkowitej puli woluminu*  
    Jest to suma przyrostowe przestrzeni logicznej używanej przez migawki.  

## <a name="volumes"></a>Metryki użycia dla woluminów

- *Przydzielony rozmiar woluminu*   
    Jest to rozmiar woluminu (przydział) udostępniane w GiB.  
- *Rozmiar logiczny woluminu*   
    Jest to łączne miejsce logiczne używane na woluminie (GiB). Ten rozmiar obejmuje logicznej przestrzeni używanej przez systemy plików aktywnych i migawek.  
- *Rozmiar migawki woluminu*   
    Jest to przyrostowe przestrzeni logicznej używanej przez migawek w woluminie.  

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
