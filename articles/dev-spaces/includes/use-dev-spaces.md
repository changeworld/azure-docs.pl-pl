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
ms.openlocfilehash: 2563f7c36283521541562bcd88f973d86a6f672a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurowanie klastra AKS używanie spacji deweloperów platformy Azure

Otwórz okno polecenia i wpisz następujące polecenia wiersza polecenia platformy Azure przy użyciu grupy zasobów, zawierającą klaster AKS i AKS nazwa klastra:

   ```cmd
   az extension add --name dev-spaces-preview 
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
Pierwsze polecenie instaluje rozszerzenie interfejsu wiersza polecenia Azure, aby dodać obsługę spacje deweloperów usługi Azure, a drugi konfiguruje klaster z obsługą spacje deweloperów usługi Azure.
