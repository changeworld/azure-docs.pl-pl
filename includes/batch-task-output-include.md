---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183584"
---
Zadanie uruchomione w usłudze Azure Batch może powodować dane wyjściowe po uruchomieniu. Dane wyjściowe zadania często muszą być przechowywane do pobierania przez inne zadania w zadaniu, aplikacji klienckiej, która wykonała zadanie lub obu. Zadania zapisują dane wyjściowe do systemu plików węzła obliczeniowego Batch, ale wszystkie dane w węźle są tracone, gdy jest ponownie zaimagedowany lub gdy węzeł opuszcza pulę. Zadania mogą mieć również okres przechowywania plików, po którym pliki utworzone przez zadanie są usuwane. Z tych powodów ważne jest, aby utrwalić dane wyjściowe zadań, które będą potrzebne później do magazynu danych, takiego jak [Usługa Azure Storage.](https://docs.microsoft.com/azure/storage/)

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
