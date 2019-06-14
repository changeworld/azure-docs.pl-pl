---
title: Wdrażanie aplikacji na platformie Azure przy użyciu platformy Habitat
description: Dowiedz się, jak i spójne wdrażanie aplikacji na maszynach wirtualnych platformy Azure i kontenerów
keywords: Azure, programu chef, metodyki devops, maszyny wirtualne, przegląd, automatyzacja habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60388815"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure przy użyciu platformy Habitat
[Habitat](https://www.habitat.sh/) to aplikacja pakowania i środowisko uruchomieniowe system razem razem jednostką wdrażania aplikacji i jego automatyzację. Spowoduje to utworzenie ultimate przenośność dla aplikacji, dzięki któremu można wdrożyć do kontenerów, maszyn wirtualnych, komputerach bez systemu operacyjnego lub PaaS bez nadpisania lub ponowne pakowanie.

W tym artykule opisano główne korzyści z używania platformy Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernizacja i przenoszenie starszych aplikacji
Bezpłatne starszych aplikacji z starsze systemy operacyjne i oprogramowanie pośredniczące przez ponowne pakowanie je za pomocą systemu Habitat Wynikowy artefakt jest przenośny, i łatwo replatforms na nowsze infrastruktury, takich jak maszyny wirtualne i kontenery działające w chmurze.

## <a name="accelerate-container-adoption"></a>Przyspieszać wdrażanie kontenera
Habitat rozwiązuje ciągłe wdrażanie złożonych aplikacji opartej na mikrousługach, wyrażając dokładnie zależności środowiska uruchomieniowego. Przekraczaj proste wdrożenie niebieski i zielony poszczególnych składników i architektury zachowanie zaawansowanego wdrażania bez generowania organizowanie złożonych przepływów.

## <a name="run-any-application-anywhere"></a>Uruchamianie dowolnej aplikacji w dowolnym miejscu
Za pomocą systemu Habitat aplikacje mogą być uruchamiane zostały zmodyfikowane w dowolnym środowisku uruchomieniowym. Obejmuje to wszystko, co z komputerów bez systemu operacyjnego i maszyn wirtualnych do kontenerów (takich jak platforma Docker), systemów zarządzania klastrami (na przykład Mesosphere lub Kubernetes) i systemów PaaS (np. rozwiązanie Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrowanie przepływ pracy DevOps programu Chef
Projekt platformy Habitat to jedna z projekt typu open source, od oprogramowania Chef społeczności silne pomocy technicznej. Habitat wykorzystuje doświadczenie związane z programem Chef z automatyzację infrastruktury, aby udostępnić możliwości automatyzacji niezrównaną na potrzeby aplikacji. Program chef oferty komercyjnej pomocy technicznej dla platformy Habitat i kompilacje bezproblemową integrację między Habitat i Chef Automate, aby w pełni zautomatyzować cykl wersji aplikacji, od projektowania do wdrożenia.

## <a name="next-steps"></a>Kolejne kroki

* [Spróbuj Habitat](https://www.habitat.sh/learn/)
