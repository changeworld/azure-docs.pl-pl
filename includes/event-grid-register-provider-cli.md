---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a7ed21d8246b618149aa0d116070a14b033d5370
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869648"
---
## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli usługa Event Grid nie była wcześniej używana w subskrypcji platformy Azure, może być konieczne zarejestrowanie dostawcy zasobów usługi Event Grid. Uruchom następujące polecenie:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Ukończenie rejestracji może chwilę potrwać. Aby sprawdzić stan, uruchom polecenie:

```azurecli-interactive
az provider show -n Microsoft.EventGrid --query "registrationState"
```

Gdy właściwość `registrationState` uzyska wartość `Registered`, można kontynuować.