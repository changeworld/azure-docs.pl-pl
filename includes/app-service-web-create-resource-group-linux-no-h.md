---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fab0258b0c0e2f9b31358075a8f7a5be0228a5e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095715"
---
[!INCLUDE [resource group intro text](resource-group.md)]

W usłudze Cloud Shell utwórz grupę zasobów za pomocą polecenia [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *West Europe* (Europa Zachodnia). Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w systemie Linux w warstwie **Podstawowa**, uruchom polecenie [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie. 

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.