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
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750093"
---
Zadanie uruchamiania w usłudze Azure Batch mogą generować dane wyjściowe po jego uruchomieniu. Zadanie dane wyjściowe często musi być przechowywane przez pobieranie przez inne zadania w ramach zadania wykonywanego zadania i / lub aplikacji klienta. Zadań zapisu danych wyjściowych do systemu plików z węzła obliczeniowego usługi Batch, ale wszystkie dane w węźle zostaną utracone w przypadku, gdy go jest odtwarzany z obrazu lub gdy węzeł opuści pulę. Zadania mogą również mieć plik okres przechowywania, po którym plików utworzonych przez zadanie są usuwane. Z tego względu warto utrwalanie danych wyjściowych zadania, które będą będą potrzebne później do magazynu danych takich jak [usługi Azure Storage](https://docs.microsoft.com/azure/storage/).

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
