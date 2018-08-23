---
title: Jak używać narzędzia kubectl za pomocą usługi Azure Dev miejsca do magazynowania | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 978274a6059a327c8596df6776eaa47a27ea4a34
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "42057555"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Narzędzia kubectl za pomocą usługi Azure Dev miejsca

Dostęp klastra Kubernetes w usłudze Azure Dev przestrzeni i użyć istniejącego rozwiązania Kubernetes narzędzi takich jak `kubectl`.

Uruchamianie `az aks use-dev-spaces` polecenia spowoduje automatyczne dodanie `kubectl` kontekstu konfiguracji dla siebie, więc kubectl już powinny być połączone z klastrem usługi Azure Dev miejsca do magazynowania w usłudze Kubernetes. Przykłady:
- Upewnij się, bieżącego kontekstu: `kubectl config current-context`
- Lista wszystkich dostępnych kontekstach: `kubectl config get-contexts`. 
- Zmień kontekst: `kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny platformy Kubernetes: Uruchom `kubectl proxy`, następnie otwórz przeglądarkę, aby adres, który emituje to polecenie (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego rozwiązania Kubernetes).
- Listy uruchomionych usług w obszarze Azure Dev miejsca do magazynowania domyślną o nazwie *domyślne*: `kubectl get services --namespace=default`

