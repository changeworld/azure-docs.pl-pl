---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 74d96952d6cafb6bd7bb16ccf2f4d69b4fbd6de3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40233969"
---
## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli usługa Event Grid nie była wcześniej używana w subskrypcji platformy Azure, może być konieczne zarejestrowanie dostawcy zasobów usługi Event Grid. Uruchom następujące polecenie, aby zarejestrować dostawcę:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Ukończenie rejestracji może chwilę potrwać. Aby sprawdzić stan, uruchom polecenie:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Gdy właściwość `registrationState` uzyska wartość `Registered`, można kontynuować.