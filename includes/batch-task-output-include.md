---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7cee3f534e4ab4973a87a9c373a5b83aa2ba9ce2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549949"
---
Zadanie uruchamiania w usłudze Azure Batch mogą generować dane wyjściowe po jego uruchomieniu. Zadanie dane wyjściowe często musi być przechowywane przez pobieranie przez inne zadania w ramach zadania wykonywanego zadania i / lub aplikacji klienta. Zadań zapisu danych wyjściowych do systemu plików z węzła obliczeniowego usługi Batch, ale wszystkie dane w węźle zostaną utracone w przypadku, gdy go jest odtwarzany z obrazu lub gdy węzeł opuści pulę. Zadania mogą również mieć plik okres przechowywania, po którym plików utworzonych przez zadanie są usuwane. Z tego względu warto utrwalanie danych wyjściowych zadania, które będą będą potrzebne później do magazynu danych takich jak [usługi Azure Storage](https://docs.microsoft.com/azure/storage/).

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
