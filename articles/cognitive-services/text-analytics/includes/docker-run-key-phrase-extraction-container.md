---
title: Uruchom przykład kontenera polecenia uruchamiania platformy docker
titleSuffix: Azure Cognitive Services
description: Polecenie Uruchamianie platformy Docker dla kontenera wyodrębniania fraz kluczowych
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ed42d5b0461b6273c2f8d84a267b65461d160ef
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877114"
---
Aby uruchomić kontener *wyodrębniania frazy klucza,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *wyodrębniania frazy kluczowej* z obrazu kontenera
* Przydziela jeden rdzeń procesora i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.