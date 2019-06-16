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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127521"
---
Zadanie uruchamiania w usłudze Azure Batch mogą generować dane wyjściowe po jego uruchomieniu. Zadanie dane wyjściowe często musi być przechowywane przez pobieranie przez inne zadania w ramach zadania wykonywanego zadania i / lub aplikacji klienta. Zadań zapisu danych wyjściowych do systemu plików z węzła obliczeniowego usługi Batch, ale wszystkie dane w węźle zostaną utracone w przypadku, gdy go jest odtwarzany z obrazu lub gdy węzeł opuści pulę. Zadania mogą również mieć plik okres przechowywania, po którym plików utworzonych przez zadanie są usuwane. Z tego względu warto utrwalanie danych wyjściowych zadania, które będą będą potrzebne później do magazynu danych takich jak [usługi Azure Storage](https://docs.microsoft.com/azure/storage/).

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
