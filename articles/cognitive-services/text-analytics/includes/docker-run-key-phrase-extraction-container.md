---
title: Przykład uruchomienia kontenera dla polecenia Docker Run
titleSuffix: Azure Cognitive Services
description: Polecenie Docker Run dla kontenera wyodrębnianie kluczowych fraz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 07af07463381e319b018baf44aa0b3a79b6d6bf8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051312"
---
### <a name="run-container-example-of-docker-run-command"></a>Przykład uruchomienia kontenera dla polecenia Docker Run

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *wyodrębnianie kluczowych fraz* kontener z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.