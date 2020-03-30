---
title: Jak używać kubectl z usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Dowiedz się, jak używać poleceń kubectl w obszarze deweloperskim w klastrze usługi Azure Kubernetes z włączoną usługą Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438360"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Używanie kubectl z przestrzenią deweloperów platformy Azure

Można uzyskać dostęp do klastra Kubernetes w ramach obszaru deweloperów `kubectl`platformy Azure i korzystać z istniejących narzędzi Kubernetes, takich jak .

Uruchomione `az aks use-dev-spaces` polecenie automatycznie doda `kubectl` kontekst konfiguracji, więc kubectl powinien być już połączony z klastrem azure dev spaces kubernetes. Przykłady:
- Potwierdź bieżący kontekst:`kubectl config current-context`
- Wyświetl listę wszystkich `kubectl config get-contexts`dostępnych kontekstów: . 
- Zmień kontekst:`kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny kubernetes: uruchom `kubectl proxy`, a następnie otwórz przeglądarkę `/ui` na adres, który emituje to polecenie (dołącz do adresu URL, aby przejść do pulpitu nawigacyjnego Kubernetes).
- Wyświetl listę uruchomionych usług w domyślnym obszarze azure dev spaces o nazwie *domyślna:*`kubectl get services --namespace=default`

