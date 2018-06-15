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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31532502"
---
Zadanie uruchomione w partii zadań Azure może tworzyć dane wyjściowe po uruchomieniu. Zadanie dane wyjściowe często wymaga mają być przechowywane do pobrania przez inne zadania w zadaniu aplikacji klienckiej, która jest wykonywane zadanie lub oba. Zadania zapisywanie danych wyjściowych w systemie pliku wsadowego węzła obliczeń, ale wszystkie dane w węźle zostaną utracone w przypadku, gdy jest on odtworzyć z obrazu lub gdy węzeł opuszcza puli. Zadania mogą także mieć okresu przechowywania plików, po upływie którego pliki utworzonej przez zadanie są usuwane. Z tego względu, ważne jest, aby utrwalić danych wyjściowych zadania, które będą potrzebne później w magazynie danych takich jak [usługi Azure Storage](https://docs.microsoft.com/azure/storage/).

Dla opcji konta magazynu w partii, zobacz [Przegląd funkcji partii](../articles/batch/batch-api-basics.md#azure-storage-account).
