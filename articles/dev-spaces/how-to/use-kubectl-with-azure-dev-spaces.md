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
description: Szybkie opracowywanie Kubernetes z kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, usługa Azure Kubernetes, kontenerów
manager: douge
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Użyj kubectl z przestrzenią deweloperów platformy Azure

Można uzyskać dostępu do klastra Kubernetes w przestrzeni deweloperów usługi Azure i użyj istniejącego Kubernetes narzędzi, takich jak `kubectl`.

Uruchomiona `azds resource create` lub `azds resource select` automatycznie doda `kubectl` kontekstu konfiguracji dla Ciebie, więc kubectl już powinny być podłączone do klastra Kubernetes spacje deweloperów platformy Azure. Przykłady:
- Upewnij się, bieżącego kontekstu: `kubectl config current-context`
- Wyświetl listę wszystkich dostępnych kontekstach: `kubectl config get-contexts`. 
- Kontekst zmiany: `kubectl config use-context <context-name>`
- Wyświetlić pulpit nawigacyjny Kubernetes: Uruchom `kubectl proxy`, Otwórz w przeglądarce adres, który emituje to polecenie (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego Kubernetes).
- Listy uruchomionych usług w domyślna przestrzeń spacje deweloperów Azure o nazwie *domyślne*: `kubectl get services --namespace=default`

