---
title: Przykłady wyodrębnianie kluczowych fraz Docker kontenera
titleSuffix: Azure Cognitive Services
description: Przykłady wyodrębnianie kluczowych fraz Docker kontenera
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: bc0375369db351038c7ac550cbe51415a0b3e069
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148461"
---
### <a name="key-phrase-extraction-container-docker-examples"></a>Przykłady wyodrębnianie kluczowych fraz Docker kontenera

Poniższe przykłady platformy Docker dotyczą kontenera wyodrębnianie kluczowych fraz.

#### <a name="basic-example"></a>Podstawowy przykład 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
