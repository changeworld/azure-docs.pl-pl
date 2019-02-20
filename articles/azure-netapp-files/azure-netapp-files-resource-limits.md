---
title: Limity zasobów dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano limity dla zasobów usługi Azure Files NetApp, w tym limity dla konta, pojemności pul, woluminy, migawki i delegowanego podsieci NetApp.
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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 196d85917e0a9900e141d58bff171beeb8540409
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430017"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Opis limity zasobów dla usługi Azure Files NetApp ułatwia zarządzanie woluminami.

- Każda subskrypcja platformy Azure może mieć maksymalnie 10 kont NetApp.
- Każde konto NetApp może mieć maksymalnie 25 pule pojemności.
- Każda pula pojemności może należeć tylko do jednego konta usługi NetApp.  
- Minimalny rozmiar pojedynczej puli pojemności wynosi 4 TiB, a maksymalny rozmiar wynosi 500 TiB. 
- Każda pojemność puli nie może przekraczać 500 woluminów.
- Minimalny rozmiar jednego woluminu wynosi 100 GiB, a maksymalny rozmiar wynosi 92 TiB.
- Każdego woluminu nie może przekraczać 255 migawek.
- Każda sieć wirtualna platformy Azure (Vnet) może mieć tylko jedną podsieć delegować domenę do usługi Azure Files NetApp.

**Następne kroki**

[Zrozumienie hierarchii magazynu usługi Azure Files NetApp](azure-netapp-files-understand-storage-hierarchy.md)
