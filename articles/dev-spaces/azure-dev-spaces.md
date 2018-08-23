---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 14b51cc2ad2e8e0f294e5e73e542001e30d21c9d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41921040"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Usługa Azure Dev Spaces udostępnia środowisko do szybkiego, iteracyjnego programowania na platformie Kubernetes dla zespołów. W przypadku minimalnej konfiguracji maszyny deweloperskiej możesz iteracyjnie uruchamiać i debugować kontenery bezpośrednio w usłudze Azure Kubernetes Service (AKS). Programuj na komputerach z systemem Windows, Mac lub Linux, używając znanych narzędzi, takich jak Visual Studio, Visual Studio Code czy wiersz polecenia.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>W jaki sposób usługa Azure Dev Spaces upraszcza opracowywanie kontenerów Kubernetes 

Usługa Azure Dev Spaces ułatwia zespołom deweloperów zwiększenie produktywności na platformie Kubernetes w następujący sposób:
- Zminimalizowanie konfiguracji lokalnej maszyny deweloperskiej dla każdego członka zespołu i praca bezpośrednio w usłudze AKS, zarządzanym klastrze Kubernetes na platformie Azure.
- Błyskawiczna iteracja i debugowanie kodu bezpośrednio na platformie Kubernetes przy użyciu programu Visual Studio 2017 lub Visual Studio Code.
- Generowanie zasobów konfiguracji jako kodu Docker i Kubernetes do użytku od etapu programowania do etapu produkcji. 
- Udostępnianie zespołowi zarządzanego klastra Kubernetes i wspólna praca. Opracowywanie kodu w izolacji i wykonywanie kompleksowych testów z innymi składnikami bez konieczności replikowania i pozorowania zależności.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>Zobacz też

[Azure Kubernetes Service](/azure/aks)