---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198762"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Usługa Azure Dev Spaces ułatwia szybkie opracowywanie w rozwiązaniu Kubernetes. Korzystając z usługi Azure Dev Spaces, można również dodawać pełne możliwości programistyczne, takie jak debugowanie w kontenerach usługi Azure Kubernetes. Można również iteracyjnie programować kontenery w chmurze przy użyciu znanych narzędzi, takich jak VS Code, Visual Studio czy wiersz polecenia. Usługa Azure Dev Spaces jest szczególnie istotna w przypadku programowania zespołowego, gdzie izolacja poszczególnych rozgałęzień kodu w ich własnych obszarach stanowi krytyczny element cyklu projektowania.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>W jaki sposób usługa Azure Dev Spaces upraszcza opracowywanie kontenerów Kubernetes 

Takie podejście zapewnia wiele korzyści:

* Uzyskanie środowiska projektowego bez infrastruktury, które jest reprezentatywne dla środowiska produkcyjnego, z pełnym dostępem do zasobów w chmurze.
* Debugowanie kontenerów Node.js i .NET Core bezpośrednio w usłudze Kubernetes za pomocą programu VS Code lub Visual Studio. We wszystkich innych językach można programować przy użyciu interfejsu wiersza polecenia.
* Udostępnianie wystąpienia usługi Kubernetes w zespole deweloperów w celu ograniczenia kosztów i zminimalizowania pracy związanej z konfigurowaniem lokalnego komputera dla nowych członków zespołu.
* Opracowywanie kodu w izolacji i wykonywanie kompleksowych testów z innymi składnikami bez konieczności replikowania i pozorowania zależności.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)