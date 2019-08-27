---
title: Wypychanie platformy Docker dla kontenera wykrywanie języka
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera wykrywanie języka
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051301"
---
## <a name="pull-the-language-detection-container"></a>Ściąganie kontenera wykrywanie języka

Obrazy kontenerów dla analiza tekstu są dostępne w programie Microsoft Container Registry.

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|-----------|------------|
| Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759) w usłudze Docker Hub.


### <a name="docker-pull-for-the-language-detection-container"></a>Wypychanie platformy Docker dla kontenera wykrywanie języka

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
