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
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452640"
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

[Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
