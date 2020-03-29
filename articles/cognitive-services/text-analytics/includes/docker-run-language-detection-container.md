---
title: Uruchom przykład kontenera polecenia uruchamiania platformy docker
titleSuffix: Azure Cognitive Services
description: Polecenie Uruchamianie platformy Docker dla kontenera wykrywania języka
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: d2818336de2b3d4d810932cefb21edd95b8cf733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966668"
---
Aby uruchomić kontener *wykrywania języka,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *wykrywania języka* z obrazu kontenera
* Przydziela jeden rdzeń procesora i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.