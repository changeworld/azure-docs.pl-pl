---
title: Programowanie zespołowe za pomocą spacji deweloperów platformy Azure przy użyciu języka Java i programu VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s '
manager: mmontwil
ms.openlocfilehash: 3f7a7b5c9a22ba9cb8746cecde56c0a047521ad0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437651"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Wprowadzanie zmiany w kodzie
Przejdź do okna programu VS Code `mywebapi` i kodu, Edytuj, aby `String index()` method in Class metoda `src/main/java/com/ms/sample/mywebapi/Application.java`, na przykład:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
