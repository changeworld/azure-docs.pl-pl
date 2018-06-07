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
ms.openlocfilehash: 7f4dced6f82622ba735b1b059f30d88830347fba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625852"
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurowanie klastra AKS używanie spacji deweloperów platformy Azure

Otwórz okno polecenia i wprowadź następujące polecenie wiersza polecenia platformy Azure przy użyciu grupy zasobów, zawierającą klaster AKS i AKS nazwa klastra. Polecenie konfiguruje klaster z obsługą spacje deweloperów platformy Azure.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

