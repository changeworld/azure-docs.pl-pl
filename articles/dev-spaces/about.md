---
title: Wprowadzenie do Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Wprowadzenie do Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: 08214e4be866da592e4852ef718ee82fa2a27d08
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325790"
---
# <a name="introduction-to-azure-dev-spaces"></a>Wprowadzenie do Azure Dev Spaces

Azure Dev Spaces zapewnia szybkie, iteracyjne środowisko programistyczne Kubernetes dla zespołów w klastrach usługi Azure Kubernetes Service (AKS). Azure Dev Spaces umożliwia również debugowanie i testowanie wszystkich składników aplikacji w AKS z minimalną konfiguracją maszyny deweloperskiej bez replikowania lub tworzenia zależności.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>W jaki sposób usługa Azure Dev Spaces upraszcza opracowywanie kontenerów Kubernetes

Azure Dev Spaces ułatwia zespołom skoncentrowanie się na opracowywaniu i szybkiej iteracji aplikacji mikrousług przez umożliwienie zespołom pracy bezpośrednio z całą architekturą mikrousług lub aplikacją działającą w AKS. Azure Dev Spaces również umożliwia niezależne aktualizowanie części architektury mikrousług w izolacji bez wpływu na resztę klastra AKS lub innych deweloperów. Azure Dev Spaces jest przeznaczony do tworzenia i testowania w środowiskach deweloperskich i testowych niższego poziomu i nie jest przeznaczony do uruchamiania w produkcyjnych klastrach AKS.

Ponieważ zespoły mogą współdziałać z całą aplikacją i współpracować bezpośrednio w AKS, Azure Dev Spaces:

* Minimalizuje konfigurację komputera lokalnego
* Skraca czas instalacji dla nowych deweloperów w zespole
* Zwiększa prędkość zespołu przez szybszą iterację
* Zmniejsza liczbę nadmiarowych środowisk programistycznych i integracji, ponieważ członkowie zespołu mogą współużytkować klaster
* Eliminuje konieczność replikowania lub makietowania zależności
* Usprawnia współpracę między zespołami programistycznymi, a także z zespołami, z którymi pracują, takich jak zespoły DevOps

Azure Dev Spaces udostępnia narzędzia do generowania zasobów platformy Docker i Kubernetes dla projektów. To narzędzie umożliwia łatwe dodawanie nowych i istniejących aplikacji do przestrzeni deweloperskiej i innych klastrów AKS.

Aby uzyskać więcej informacji na temat działania Azure Dev Spaces, zobacz [How to Azure dev Spaces działa i jest skonfigurowana][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Obsługiwane regiony i konfiguracje

Azure Dev Spaces jest obsługiwana tylko przez klastry AKS w **regionach Wschodnie stany USA**, **Wschodnie stany USA 2**, **środkowe stany USA**, **Południowo-środkowe stany USA**, **zachodnie**stany USA 2, **Europa Północna**, **Europa Zachodnia**, **Południowe Zjednoczone Królestwo**, **Azja Wschodnia**, **Azja Południowo**-Wschodnia, **Australia Wschodnia**, **Kanada środkowa**i **Kanada Wschodnia** . Usługa Azure Dev Spaces obsługuje korzystanie z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub programu [Visual Studio Code](https://code.visualstudio.com/download) za pomocą [rozszerzenia usługi Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) zainstalowanego w systemie Linux, MacOS lub Windows 8 bądź nowszym, aby umożliwić tworzenie i uruchamianie aplikacje w usłudze AKS. Obsługuje ona również korzystanie z [programu Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) zainstalowanego w systemie Windows 8 lub nowszym. W przypadku programu Visual Studio 2019 będzie potrzebne obciążenie Programowanie na platformie Azure. W przypadku programu Visual Studio 2017 będzie potrzebne obciążenie programowanie sieci Web i [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o szybkim, iteracyjnym programowaniu dla zespołów, które Azure Dev Spaces z zespołem szybki start dla deweloperów.

> [!div class="nextstepaction"]
> [Programowanie zespołowe — Szybki Start](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
