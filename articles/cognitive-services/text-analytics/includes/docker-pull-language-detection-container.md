---
title: Ściąganie platformy Docker dla kontenera wykrywania języka
titleSuffix: Azure Cognitive Services
description: Polecenie ściągania platformy Docker dla kontenera wykrywania języka
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877105"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Ściąganie platformy Docker dla kontenera wykrywania języka

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analizy tekstu, zobacz kontener [wykrywania języka](https://go.microsoft.com/fwlink/?linkid=2018759) w Centrum platformy Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
