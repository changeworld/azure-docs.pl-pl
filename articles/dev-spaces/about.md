---
title: Wprowadzenie do usługi Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Wprowadzenie do usługi Azure Dev miejsca do magazynowania
keywords: Docker, Kubernetes, Azure, usługi AKS, usługi Azure Kubernetes Service, kontenerów, narzędzia kubectl, k8s
manager: gwallace
ms.openlocfilehash: 33ac5a7aa6d823105b87325ba52aa77cd9b9b3a3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706300"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Usługa Azure Dev spacji jest szybkie, iteracyjne Kubernetes, środowisko programistyczne dla zespołów w klastrach usługi Azure Kubernetes Service (AKS). Możesz współpracować z zespołem w udostępnionym klastra AKS. Usługa Azure Dev spacje umożliwia również przetestować wszystkie składniki aplikacji w usłudze AKS bez replikacji lub pozorowanie się zależności. Iteracyjne można uruchomić i debugować kontenerów bezpośrednio w usłudze AKS za pomocą minimalnej deweloperskim maszyny.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>W jaki sposób usługa Azure Dev Spaces upraszcza opracowywanie kontenerów Kubernetes

Usługa Azure Dev do magazynowania ułatwia zespołom skoncentrowanie się na rozwoju i szybkie iteracji w swojej aplikacji mikrousług, umożliwiając zespołom pracować bezpośrednio z ich architektury mikrousług całego lub aplikacja działająca w usłudze AKS. Usługa Azure Dev spacje umożliwia również niezależnie aktualizować części architektury mikrousług w izolacji bez wpływu na pozostałe klastra AKS lub innym deweloperom. Usługa Azure Dev spacji jest do programowania i testowania w rozwoju niższego poziomu i środowisk testowych i nie jest przeznaczony do uruchamiania w klastrach usługi AKS w środowisku produkcyjnym.

Ponieważ zespoły mogą pracować z całej aplikacji i współpracować bezpośrednio w usłudze AKS i Azure Dev miejsca do magazynowania:

* Minimalizuje instalacji na komputerze lokalnym
* Zmniejsza się Konfigurowanie czasu dla nowych deweloperów w zespole
* Zwiększa szybkość pracy zespołu za pośrednictwem szybciej iteracji
* Zmniejsza liczbę nadmiarowych środowisk programowania i integracji, ponieważ członkowie zespołu mogą współużytkować klaster
* Usuwa potrzebę replikacji lub makiety zależności
* Zwiększa współpracę zespołów programistycznych, a także zespoły, z których korzystają użytkownicy, takich jak zespoły DevOps

Usługa Azure Dev spacje udostępnia narzędzia do generowania zasoby platformy Docker i Kubernetes dla Twoich projektów. Tych narzędzi umożliwia łatwe dodawanie nowych i istniejących aplikacji do miejsca deweloperów i inne klastry usługi AKS.

Aby uzyskać więcej informacji na temat działania usługi Azure Dev miejsca do magazynowania, zobacz [jak Azure Dev miejsca do magazynowania działa i jest skonfigurowany][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Obsługiwane regiony i konfiguracji

Usługa Azure Dev Spaces jest obsługiwana tylko w klastrach usługi AKS w następujących regionach: **Wschodnie stany USA**, **Wschodnie stany USA 2**, **Środkowe stany USA**, **Zachodnie stany USA 2**, **Europa Północna**, **Europa Zachodnia**, **Południowe Zjednoczone Królestwo**, **Azja Południowo-Wschodnia**, **Australia Wschodnia**, **Kanada Środkowa** i **Kanada Wschodnia**. Usługa Azure Dev Spaces obsługuje korzystanie z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub programu [Visual Studio Code](https://code.visualstudio.com/download) za pomocą [rozszerzenia usługi Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) zainstalowanego w systemie Linux, MacOS lub Windows 8 bądź nowszym, aby umożliwić tworzenie i uruchamianie aplikacje w usłudze AKS. Obsługuje ona również, za pomocą [programu Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) zainstalowanych w systemie Windows 8 lub nowszej. Dla programu Visual Studio 2019 r konieczne będzie obciążenia programowanie na platformie Azure. Dla programu Visual Studio 2017, konieczne będzie obciążenia programowanie dla sieci Web i [Visual Studio Tools dla platformy Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o programowaniu szybkie, iteracyjne dla zespołów za pomocą usługi Azure Dev miejsca do magazynowania przy użyciu opcji szybkiego startu tworzenia zespołu.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start zespołu rozwoju](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
