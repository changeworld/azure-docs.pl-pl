---
title: Jak używać polecenia kubectl z Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: fea14668c35eddcd01417b417e2239788eddd6d1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279943"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Korzystanie z polecenia kubectl z przestrzenią dev platformy Azure

Możesz uzyskać dostęp do klastra Kubernetes w przestrzeni usługi Azure dev i korzystać z istniejących narzędzi Kubernetes, takich jak `kubectl`.

Uruchomienie `az aks use-dev-spaces` polecenie spowoduje automatyczne dodanie kontekstu konfiguracji `kubectl`, więc polecenia kubectl powinny być już połączone z klastrem Azure Dev Spaces Kubernetes. Przykłady:
- Potwierdź bieżący kontekst: `kubectl config current-context`
- Wyświetl wszystkie dostępne konteksty: `kubectl config get-contexts`. 
- Zmień kontekst: `kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny Kubernetes: Uruchom `kubectl proxy`, a następnie otwórz przeglądarkę na adres, który to polecenie emituje (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego Kubernetes).
- Wyświetl listę uruchomionych usług w domyślnym obszarze Azure Dev Spaces nazwa *Domyślna*: `kubectl get services --namespace=default`

