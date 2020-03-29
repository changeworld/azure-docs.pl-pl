---
title: Ściąganie platformy Docker dla kontenera Analiza tonacji
titleSuffix: Azure Cognitive Services
description: Polecenie ściągania platformy Docker dla kontenera Analiza tonacji
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966685"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Ściąganie platformy Docker dla kontenera Analiza tonacji

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analizy tekstu, zobacz kontener [analizy tonacji](https://go.microsoft.com/fwlink/?linkid=2018654) w Centrum platformy Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
