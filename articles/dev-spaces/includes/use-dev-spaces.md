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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991008"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurowanie klastra usługi AKS do korzystania z usługi Azure Dev Spaces

Otwórz okno polecenia i wpisz następujące polecenie interfejsu wiersza polecenia platformy Azure, podając grupę zasobów, która zawiera klaster usługi AKS oraz nazwę klastra usługi AKS. Polecenie konfiguruje na klastrze obsługę usługi Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

