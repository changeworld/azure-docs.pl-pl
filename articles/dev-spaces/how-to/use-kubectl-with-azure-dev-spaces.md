---
title: Jak używać narzędzia kubectl za pomocą usługi Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s '
ms.openlocfilehash: 328c67b04f8e582fcd7d953c7cd8b6c5312bada5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194318"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Narzędzia kubectl za pomocą usługi Azure Dev miejsca

Dostęp klastra Kubernetes w usłudze Azure Dev przestrzeni i użyć istniejącego rozwiązania Kubernetes narzędzi takich jak `kubectl`.

Uruchamianie `az aks use-dev-spaces` polecenia spowoduje automatyczne dodanie `kubectl` kontekstu konfiguracji dla siebie, więc kubectl już powinny być połączone z klastrem usługi Azure Dev miejsca do magazynowania w usłudze Kubernetes. Przykłady:
- Upewnij się, bieżącego kontekstu: `kubectl config current-context`
- Lista wszystkich dostępnych kontekstach: `kubectl config get-contexts`. 
- Zmień kontekst: `kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny platformy Kubernetes: Uruchom `kubectl proxy`, następnie otwórz przeglądarkę, aby adres, który emituje to polecenie (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego rozwiązania Kubernetes).
- Listy uruchomionych usług w obszarze Azure Dev miejsca do magazynowania domyślną o nazwie *domyślne*: `kubectl get services --namespace=default`

