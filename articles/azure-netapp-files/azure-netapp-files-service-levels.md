---
title: Poziomów usług dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano wydajność przepustowości dla poziomów usług Azure Files NetApp.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523105"
---
# <a name="service-levels-for-azure-netapp-files"></a>Poziomy usług dla usługi Azure NetApp Files
Poziomy usług są atrybut pojemności puli. Poziomy usług są zdefiniowane i zróżnicowane według dozwoloną maksymalną przepustowość dla woluminu w puli pojemność, w oparciu o przydział, który jest przypisany do woluminu.

## <a name="supported-service-levels"></a>Poziomy usług obsługiwanych

Usługa Azure Files NetApp obsługuje trzy poziomy usług: *Największa*, *Premium*, i *standardowa*. 

* <a name="Ultra"></a>Największa magazynu

    Warstwy magazynowania Ultra oferuje do 128 MiB/s przepływności na 1 TiB przypisane limit przydziału woluminu. 

* <a name="Premium"></a>Usługa Premium storage

    Warstwa magazynu Premium oferuje maksymalnie 64 MiB/s przepływności na 1 TiB przypisane limit przydziału woluminu. 

* <a name="Standard"></a>Magazynu w warstwie standardowa

    Warstwa magazynu w warstwie standardowa zapewnia się maksymalnie 16 MiB/s przepływności na 1 TiB przypisane limit przydziału woluminu.

## <a name="throughput-limits"></a>Limity przepustowości

Limit przepływności dla woluminu jest określany przez kombinację następujących czynników:
* Usługi poziomu pojemność puli, do której należy dany wolumin
* Limit przydziału przypisanych do woluminu  

Pojęcie to zostało zilustrowane na poniższym diagramie:

![Ilustracja poziomu usługi](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

W przykładzie 1 powyżej woluminu z puli pojemności w warstwie Premium storage, przypisana 2 TiB limit przydziału zostanie przypisany limit przepływności 128 MiB/s (2 TiB * 64 MiB/s). Ten scenariusz dotyczy niezależnie od rozmiaru puli pojemność i użycie rzeczywistej objętości.

W przykładzie 2 powyżej, wolumin z puli pojemności w warstwie Premium magazyn, którą przypisano 100 GiB limitu przydziału zostanie przypisany limit przepływności 6.25 MiB/s (0.09765625 TiB * 64 MiB/s). Ten scenariusz dotyczy niezależnie od rozmiaru puli pojemność i użycie rzeczywistej objętości.

## <a name="next-steps"></a>Kolejne kroki

- Informacje o różnych poziomach usługi można uzyskać na [stronie z cennikiem dla usługi Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Zobacz [modelu kosztów dla usługi Azure Files NetApp](azure-netapp-files-cost-model.md) obliczania poziom zużycia pojemności w puli pojemności 
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)