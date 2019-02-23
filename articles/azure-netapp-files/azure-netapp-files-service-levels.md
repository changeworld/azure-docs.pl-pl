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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668348"
---
# <a name="service-levels-for-azure-netapp-files"></a>Poziomy usług dla usługi Azure NetApp Files
Usługa Azure Files NetApp obsługuje dwa poziomy usług: Premium i standardowa. 

## <a name="Premium"></a>Usługa Premium storage

*Premium* storage oferuje maksymalnie 64 MiB/s na TiB przepływności. Wydajność przepustowości jest indeksowana względem limit przydziału woluminu. Na przykład na woluminie z usługi Premium storage przy użyciu 2 TiB elastycznie limit przydziału (niezależnie od rzeczywistego zużycia) jest przepływność 128 MiB/s.

## <a name="Standard"></a>Magazynu w warstwie standardowa

*Standardowa* storage oferuje maksymalnie 16 MiB/s na TiB przepływności. Wydajność przepustowości jest indeksowana względem limit przydziału woluminu. Na przykład wolumin z magazynu w warstwie standardowa przy użyciu 2 TiB elastycznie limit przydziału (niezależnie od rzeczywistego zużycia) ma przepływność 32 MiB/s.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [usługi Azure Files NetApp stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/netapp/) w cenie usługi różne poziomy
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
