---
title: Wypychanie platformy Docker dla kontenera wyodrębnianie kluczowych fraz
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera wyodrębnianie kluczowych fraz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051351"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Ściąganie kontenera wyodrębnianie kluczowych fraz

Obrazy kontenerów dla analiza tekstu są dostępne w programie Microsoft Container Registry.

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|-----------|------------|
| Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wyodrębnianie kluczowych fraz](https://go.microsoft.com/fwlink/?linkid=2018757) w usłudze Docker Hub.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Wypychanie platformy Docker dla kontenera wyodrębnianie kluczowych fraz

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
