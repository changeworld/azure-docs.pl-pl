---
title: Wdrażanie aplikacji na platformie Azure przy użyciu siedliska
description: Dowiedz się, jak stale wdrażać aplikację na maszynach wirtualnych i kontenerach platformy Azure
keywords: Azure, Chef, DevOps, Virtual Machines, przegląd, Automatyzacja, Siedlisko
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158062"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure przy użyciu siedliska
[Siedlisko](https://www.habitat.sh/) to pakiet aplikacji i system środowiska uruchomieniowego, który łączy aplikację i jej automatyzację jako jednostkę wdrożenia. Powoduje to utworzenie ostatecznej przenośności aplikacji, co pozwala na wdrożenie jej w kontenerach, maszynach wirtualnych, bez systemu operacyjnego lub PaaS, bez konieczności ponownego zapisywania ani ponownej pakowania.

W tym artykule opisano główne korzyści wynikające z używania siedliska.

## <a name="modernize-and-move-legacy-applications"></a>Modernizacja i przenoszenie starszych aplikacji
Bezpłatne starsze aplikacje ze starszych systemów operacyjnych i oprogramowania pośredniczącego przez ponowne pakowanie ich przy użyciu siedliska. Wynikający z tego artefakt jest przenośny, i łatwe odplatformowanie do nowszej infrastruktury, takich jak maszyny wirtualne lub kontenery działające w chmurze.

## <a name="accelerate-container-adoption"></a>Przyspieszenie przyjęcia kontenera
Siedlisko rozwiązuje ciągłe wdrażanie złożonych aplikacji z mikrousługą przez dokładne przedstawienie zależności środowiska uruchomieniowego. Przechodź poza prostym, niebieskim/zielonym wdrożeniem poszczególnych składników i architektem zaawansowane zachowanie wdrażania bez generowania złożonych przepływów aranżacji.

## <a name="run-any-application-anywhere"></a>Uruchamianie dowolnej aplikacji w dowolnym miejscu
Dzięki siedlisku aplikacje można uruchamiać w niezmienionej postaci w środowisku uruchomieniowym. Dotyczy to zarówno systemu operacyjnego, jak i maszyn wirtualnych, do kontenerów (takich jak Docker), systemów zarządzania klastrami (takich jak mesosphere lub Kubernetes) i systemów PaaS (takich jak Cloud Foundry przestawne).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integracja z przepływem pracy Chef DevOps
Projekt siedliska jest jednym z projektów typu "open source" z Chef Software z silną społecznością pomocy technicznej. Siedlisko wykorzystuje głębokie środowisko Chef z automatyzacją infrastruktury, aby zapewnić niespotykaną możliwości automatyzacji aplikacjom. Chef oferuje komercyjną pomoc techniczną dotyczącą siedlisk i tworzy bezproblemową integrację między siedliskiem a Chef automatyzują w celu pełnego zautomatyzowania cyklu wydania aplikacji od projektowania do wdrożenia.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj siedlisko](https://www.habitat.sh/learn/)
