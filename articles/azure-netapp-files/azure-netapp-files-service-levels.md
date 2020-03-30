---
title: Poziomy usług dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano wydajność przepływności dla poziomów usług plików NetApp platformy Azure.
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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832579"
---
# <a name="service-levels-for-azure-netapp-files"></a>Poziomy usług dla usługi Azure NetApp Files
Poziomy usług są atrybutem puli pojemności. Poziomy usług są definiowane i różnicowane przez dozwoloną maksymalną przepływność woluminu w puli pojemności na podstawie przydziału przypisanego do woluminu.

## <a name="supported-service-levels"></a>Obsługiwane poziomy usług

Usługa Azure NetApp Files obsługuje trzy poziomy usług: *Ultra,* *Premium*i *Standard.* 

* <a name="Ultra"></a>Pamięć ultrasechowa

    Warstwa magazynu Ultra zapewnia do 128 MiB/s przepływności na 1 TiB przypisanego przydziału woluminu. 

* <a name="Premium"></a>Pamięć masowa w jakości Premium

    Warstwa magazynu w warstwie Premium zapewnia do 64 MiB/s przepływności na 1 TiB przypisanego przydziału woluminu. 

* <a name="Standard"></a>Magazyn standardowy

    Warstwa magazynu standardowego zapewnia do 16 MiB/s przepływności na 1 TiB przypisanego przydziału woluminu.

## <a name="throughput-limits"></a>Limity przepływności

Limit przepływności dla woluminu jest określany przez kombinację następujących czynników:
* Poziom usług puli pojemności, do której należy wolumin
* Przydział przypisany do woluminu  

Koncepcja ta jest zilustrowana na poniższym diagramie:

![Ilustracja poziomu usług](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

W przykładzie 1 powyżej wolumin z puli pojemności z warstwą magazynu w warstwie Premium, która jest przypisana 2 TiB przydziału, zostanie przypisany limit przepływności 128 MiB/s (2 TiB * 64 MiB/s). Ten scenariusz ma zastosowanie niezależnie od rozmiaru puli pojemności lub rzeczywistego zużycia woluminu.

W przykładzie 2 powyżej wolumin z puli pojemności z warstwą magazynu w warstwie Premium, która jest przypisana 100 GiB przydziału, zostanie przypisany limit przepływności 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Ten scenariusz ma zastosowanie niezależnie od rozmiaru puli pojemności lub rzeczywistego zużycia woluminu.

## <a name="next-steps"></a>Następne kroki

- Informacje o różnych poziomach usługi można uzyskać na [stronie z cennikiem dla usługi Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Zobacz [Model kosztów dla plików NetApp platformy Azure, aby](azure-netapp-files-cost-model.md) obliczyć zużycie pojemności w puli pojemności 
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- Zobacz [Umowa dotyczącej poziomu usług (SLA) dla plików NetApp platformy Azure](https://azure.microsoft.com/support/legal/sla/netapp/)