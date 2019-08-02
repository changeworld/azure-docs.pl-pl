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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67183584"
---
Zadanie uruchomione w Azure Batch może generować dane wyjściowe po uruchomieniu. Dane wyjściowe zadania często muszą być przechowywane do pobrania przez inne zadania w zadaniu, aplikacja kliencka, która wykonała zadanie. Zadania zapisują dane wyjściowe w systemie plików w węźle obliczeniowym wsadowym, ale wszystkie dane w węźle są tracone podczas jego tworzenia z obrazu lub gdy węzeł opuszcza pulę. Zadania mogą także mieć okres przechowywania plików, po którym pliki utworzone przez zadanie są usuwane. Z tego względu ważne jest, aby zachować dane wyjściowe zadania, które będą potrzebne później do magazynu danych, takiego jak [usługa Azure Storage](https://docs.microsoft.com/azure/storage/).

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
