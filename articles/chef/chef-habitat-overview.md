---
title: Wdrażanie aplikacji na platformie Azure za pomocą środowiska Habitat
description: Dowiedz się, jak konsekwentnie wdrażać aplikację na maszynach wirtualnych i kontenerach platformy Azure
keywords: lazur, szef kuchni, devops, maszyny wirtualne, przegląd, automatyka, siedlisko
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158062"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure za pomocą środowiska Habitat
[Habitat](https://www.habitat.sh/) to system pakowania aplikacji i środowiska wykonawczego, który łączy aplikację i jej automatyzację jako jednostkę wdrożenia. Tworzy to ostateczną przenośność dla aplikacji, umożliwiając jej wdrożenie w kontenerach, maszynach wirtualnych, bez systemu operacyjnego lub paas bez przepisywania lub przepakowywania.

W tym artykule opisano główne korzyści wynikające z używania habitatu.

## <a name="modernize-and-move-legacy-applications"></a>Modernizowanie i przenoszenie starszych aplikacji
Darmowe starsze aplikacje ze starszych systemów operacyjnych i oprogramowania pośredniczącego, przepakowując je za pomocą habitatu. Wynikowy artefakt jest przenośny i łatwo przeplata się na nowszą infrastrukturę, taką jak maszyny wirtualne lub kontenery uruchomione w chmurze.

## <a name="accelerate-container-adoption"></a>Przyspiesz przyjęcie kontenera
Habitat rozwiązuje ciągłe wdrażanie złożonych aplikacji zorientowanych na mikrousługi, dokładnie reprezentując zależności środowiska uruchomieniowego. Wyjśnij się poza proste niebieskie/zielone wdrażanie poszczególnych składników i zaawansowane zachowanie wdrażania bez generowania złożonych przepływów aranżacji.

## <a name="run-any-application-anywhere"></a>Uruchamianie dowolnej aplikacji w dowolnym miejscu
Dzięki habitatowi aplikacje mogą działać bez zmiany w dowolnym środowisku wykonywania. Obejmuje to wszystko, od gołego metalu i maszyn wirtualnych do kontenerów (takich jak Docker), systemów zarządzania klastrami (takich jak Mesosfera lub Kubernetes) i systemów PaaS (takich jak Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integracja z przepływem pracy Programu Chef DevOps
Projekt Habitat jest jednym z projektów open source od Chef Software, z silną społecznością wsparcia. Habitat wykorzystuje głębokie doświadczenie chefa w zakresie automatyzacji infrastruktury, aby zapewnić aplikacjom bezprecedensowe możliwości automatyzacji. Chef oferuje komercyjne wsparcie dla Habitat i buduje bezproblemową integrację między Habitat i Chef Automate, aby w pełni zautomatyzować cykl wydawania aplikacji, od rozwoju do wdrożenia.

## <a name="next-steps"></a>Następne kroki

* [Spróbuj Habitat](https://www.habitat.sh/learn/)
