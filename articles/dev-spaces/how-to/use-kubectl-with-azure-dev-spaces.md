---
title: Jak używać kubectl spacjami deweloperów platformy Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 3dc0dd4b571f716bcabb67c4cbef1ea6d762eb94
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248662"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Użyj kubectl z przestrzenią deweloperów platformy Azure

Można uzyskać dostępu do klastra Kubernetes w przestrzeni deweloperów usługi Azure i użyj istniejącego Kubernetes narzędzi, takich jak `kubectl`.

Uruchomiona `az aks use-dev-spaces` automatycznie doda polecenia `kubectl` kontekstu konfiguracji dla Ciebie, więc kubectl już powinny być podłączone do klastra Kubernetes spacje deweloperów platformy Azure. Przykłady:
- Upewnij się, bieżącego kontekstu: `kubectl config current-context`
- Wyświetl listę wszystkich dostępnych kontekstach: `kubectl config get-contexts`. 
- Kontekst zmiany: `kubectl config use-context <context-name>`
- Wyświetlić pulpit nawigacyjny Kubernetes: Uruchom `kubectl proxy`, Otwórz w przeglądarce adres, który emituje to polecenie (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego Kubernetes).
- Listy uruchomionych usług w domyślna przestrzeń spacje deweloperów Azure o nazwie *domyślne*: `kubectl get services --namespace=default`

