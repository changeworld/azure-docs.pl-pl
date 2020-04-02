---
title: Co to jest usługa Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Dowiedz się, jak usługa Azure Dev Spaces zapewnia szybkie, iteracyjne środowisko programistyczne Kubernetes dla zespołów w klastrach usługi Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8b22181bcddda9e4156c0e0dbe61d7d813498d96
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529729"
---
# <a name="what-is-azure-dev-spaces"></a>Co to jest usługa Azure Dev Spaces?

Usługa Azure Dev Spaces zapewnia szybkie, iteracyjne środowisko programistyczne Kubernetes dla zespołów w klastrach usługi Azure Kubernetes (AKS). Usługa Azure Dev Spaces umożliwia również debugowanie i testowanie wszystkich składników aplikacji w usłudze AKS przy minimalnej konfiguracji komputera deweloperskiego, bez replikowania lub tworzenia szyderczych zależności.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>W jaki sposób usługa Azure Dev Spaces upraszcza opracowywanie kontenerów Kubernetes

Usługa Azure Dev Spaces pomaga zespołom skupić się na rozwoju i szybkiej iteracji aplikacji mikrousług, umożliwiając zespołom bezpośrednią pracę z całą architekturą mikrousług lub aplikacją działającą w usłudze AKS. Usługa Azure Dev Spaces zapewnia również sposób niezależnej aktualizacji części architektury mikrousług w izolacji bez wpływu na resztę klastra AKS lub innych deweloperów. Usługa Azure Dev Spaces służy do tworzenia i testowania w środowiskach deweloperskich i testowych niższego poziomu i nie jest przeznaczona do uruchamiania w produkcyjnych klastrach AKS.

Ponieważ zespoły mogą pracować z całą aplikacją i współpracować bezpośrednio w aks, Azure Dev Spaces:

* Minimalizuje konfigurację komputera lokalnego
* Skraca czas konfiguracji dla nowych programistów w zespole
* Zwiększa prędkość drużyny dzięki szybszej iteracji
* Zmniejsza liczbę nadmiarowych środowisk programowych i integracyjnych, ponieważ członkowie zespołu mogą współużytkować klaster
* Eliminuje konieczność replikowania lub mock up zależności
* Usprawnia współpracę między zespołami programistyzajdu, a także zespołami, z którymi współpracują, takimi jak zespoły DevOps

Usługa Azure Dev Spaces udostępnia narzędzia do generowania zasobów platformy Docker i Kubernetes dla projektów. To narzędzie umożliwia łatwe dodawanie nowych i istniejących aplikacji zarówno do przestrzeni deweloperskiej, jak i innych klastrów AKS.

Aby uzyskać więcej informacji na temat działania usługi Azure Dev Spaces, zobacz [Jak działa usługa Azure Dev Spaces i jest skonfigurowana.][how-dev-spaces-works]

## <a name="supported-regions-and-configurations"></a>Obsługiwane regiony i konfiguracje

Usługa Azure Dev Spaces jest obsługiwana tylko przez klastry AKS w [niektórych regionach.][supported-regions] Usługa Azure Dev Spaces obsługuje przy użyciu [interfejsu wiersza polecenia polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub kodu programu Visual [Studio](https://code.visualstudio.com/download) z [rozszerzeniem Azure Dev Spaces zainstalowanym](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) w systemie Linux, macOS lub Windows 8 lub wyższym do tworzenia i uruchamiania aplikacji w usłudze AKS. Obsługuje również przy użyciu [programu Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) zainstalowanego w systemie Windows 8 lub nowszym. W programie Visual Studio 2019 potrzebne będzie obciążenie programu Azure Development. W programie Visual Studio 2017 potrzebne będzie obciążenie web development i [narzędzia programu Visual Studio dla aplikacji Kubernetes.](https://aka.ms/get-vsk8stools)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o szybkim, iteracyjnym rozwoju dla zespołów z usługą Azure Dev Spaces dzięki [przewodnikowi szybki start rozwoju zespołu.][team-development-quickstart]

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
