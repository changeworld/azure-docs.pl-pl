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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430899"
---
# <a name="service-levels-for-azure-netapp-files"></a>Poziomy usług dla usługi Azure Files NetApp
Usługa Azure Files NetApp obsługuje dwa poziomy usług: Premium i standardowa. 

## <a name="Premium"></a>Usługa Premium storage

*Premium* storage oferuje maksymalnie 64 MiB/s na TiB przepływności. Wydajność przepustowości jest indeksowana względem limit przydziału woluminu. Na przykład na woluminie z usługi Premium storage przy użyciu 2 TiB elastycznie limit przydziału (niezależnie od rzeczywistego zużycia) jest przepływność 128 MiB/s.

## <a name="Standard"></a>Magazynu w warstwie standardowa

*Standardowa* storage oferuje maksymalnie 16 MiB/s na TiB przepływności. Wydajność przepustowości jest indeksowana względem limit przydziału woluminu. Na przykład wolumin z magazynu w warstwie standardowa przy użyciu 2 TiB elastycznie limit przydziału (niezależnie od rzeczywistego zużycia) ma przepływność 32 MiB/s.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [usługi Azure Files NetApp stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/netapp/) w cenie usługi różne poziomy
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
