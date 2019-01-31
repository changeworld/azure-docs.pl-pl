---
title: Jak używać narzędzia kubectl za pomocą usługi Azure Dev miejsca do magazynowania | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: b0ceccc4f8b16c21e8a66a5f1b8cf0e7b6f47a4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469315"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Narzędzia kubectl za pomocą usługi Azure Dev miejsca

Dostęp klastra Kubernetes w usłudze Azure Dev przestrzeni i użyć istniejącego rozwiązania Kubernetes narzędzi takich jak `kubectl`.

Uruchamianie `az aks use-dev-spaces` polecenia spowoduje automatyczne dodanie `kubectl` kontekstu konfiguracji dla siebie, więc kubectl już powinny być połączone z klastrem usługi Azure Dev miejsca do magazynowania w usłudze Kubernetes. Przykłady:
- Upewnij się, bieżącego kontekstu: `kubectl config current-context`
- Lista wszystkich dostępnych kontekstach: `kubectl config get-contexts`. 
- Zmień kontekst: `kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny platformy Kubernetes: Uruchom `kubectl proxy`, następnie otwórz przeglądarkę, aby adres, który emituje to polecenie (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego rozwiązania Kubernetes).
- Listy uruchomionych usług w obszarze Azure Dev miejsca do magazynowania domyślną o nazwie *domyślne*: `kubectl get services --namespace=default`

