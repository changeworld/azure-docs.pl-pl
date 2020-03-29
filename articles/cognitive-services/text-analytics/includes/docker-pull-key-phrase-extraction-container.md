---
title: Pobieranie docker dla kontenera wyodrębniania fraz kluczowych
titleSuffix: Azure Cognitive Services
description: Polecenie ściągania platformy Docker dla kontenera wyodrębniania fraz kluczowych
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966701"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pobieranie docker dla kontenera wyodrębniania fraz kluczowych

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analizy tekstu, zobacz kontener [wyodrębniania fraz kluczy](https://go.microsoft.com/fwlink/?linkid=2018757) w Centrum platformy Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
