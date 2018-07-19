---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 8dbc90c3888a9c53db7d2ebeea2dd5f3ee5746a0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38946083"
---
## <a name="clean-up"></a>Czyszczenie
Aby całkowicie usunąć z klastra wystąpienie usługi Azure Dev Spaces, w tym wszystkie przestrzenie deweloperów i usługi działające w ich obrębie, należy użyć polecenia `az aks remove-dev-spaces`. Pamiętaj, że ta akcja jest nieodwracalna. Możesz ponownie dodać obsługę usługi Azure Dev Spaces w klastrze, ale wszystko trzeba będzie zacząć od początku. Stare usługi i przestrzenie nie zostaną przywrócone.

Poniższy przykład wyświetla listę kontrolerów usługi Azure Dev Spaces w Twojej aktywnej subskrypcji, a następnie usuwa kontroler usługi Azure Dev Spaces, który jest skojarzony z klastrem usługi AKS „myaks” w grupie zasobów „myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

