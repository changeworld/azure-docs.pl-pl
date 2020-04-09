---
title: Pobieranie docker dla kontenera wyodrębniania fraz kluczowych
titleSuffix: Azure Cognitive Services
description: Polecenie ściągania platformy Docker dla kontenera wyodrębniania fraz kluczowych
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877125"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pobieranie docker dla kontenera wyodrębniania fraz kluczowych

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analizy tekstu, zobacz kontener [wyodrębniania fraz kluczy](https://go.microsoft.com/fwlink/?linkid=2018757) w Centrum platformy Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
