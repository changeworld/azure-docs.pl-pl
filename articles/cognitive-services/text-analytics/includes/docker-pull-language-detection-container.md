---
title: Ściąganie platformy Docker dla kontenera wykrywania języka
titleSuffix: Azure Cognitive Services
description: Polecenie ściągania platformy Docker dla kontenera wykrywania języka
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966782"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Ściąganie platformy Docker dla kontenera wykrywania języka

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

Aby uzyskać pełny opis dostępnych tagów dla kontenerów analizy tekstu, zobacz kontener [wykrywania języka](https://go.microsoft.com/fwlink/?linkid=2018759) w Centrum platformy Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
