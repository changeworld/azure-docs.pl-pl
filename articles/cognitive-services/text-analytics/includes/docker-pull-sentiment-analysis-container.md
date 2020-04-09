---
title: Ściąganie platformy Docker dla kontenera Analiza tonacji
titleSuffix: Azure Cognitive Services
description: Polecenie ściągania platformy Docker dla kontenera Analiza tonacji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877065"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Ściąganie platformy Docker dla kontenera Analiza tonacji

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analizy tekstu, zobacz kontener [analizy tonacji](https://go.microsoft.com/fwlink/?linkid=2018654) w Centrum platformy Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
