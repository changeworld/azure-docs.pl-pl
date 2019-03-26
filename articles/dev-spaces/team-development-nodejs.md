---
title: Programowanie zespołowe za pomocą usługi Azure Dev miejsca do magazynowania przy użyciu środowiska Node.js i program VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s '
ms.openlocfilehash: 5d89ac6a0a8918b30cac31580e7fc95c3f770b1a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436765"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Wprowadzanie zmiany w kodzie
Przejdź do okna programu VS Code `mywebapi` i wprowadź kod edytować domyślnych GET `/` obsługi w `server.js`, na przykład:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
