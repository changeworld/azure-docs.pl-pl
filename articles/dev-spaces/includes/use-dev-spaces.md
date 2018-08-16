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
ms.openlocfilehash: a8f5adc8e6745fdbfd31d1a9cb9407672d61da07
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129362"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurowanie klastra usługi AKS do korzystania z usługi Azure Dev Spaces

Otwórz okno polecenia i wpisz następujące polecenie interfejsu wiersza polecenia platformy Azure, podając grupę zasobów, która zawiera klaster usługi AKS oraz nazwę klastra usługi AKS. Polecenie konfiguruje na klastrze obsługę usługi Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

