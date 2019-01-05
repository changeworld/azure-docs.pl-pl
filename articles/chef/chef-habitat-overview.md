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
ms.openlocfilehash: 4847d9ce551c9acf1e4fb6325c770187b2cfd89f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052295"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure przy użyciu platformy Habitat
[Habitat](https://www.habitat.sh/) jest pierwszy jej rodzaj projektu open source, który oferuje nowe podejście do zarządzania aplikacjami. Habitat sprawia, że aplikacja i jej automatyzacji jednostką wdrożenia. Gdy aplikacje są opakowane w uproszczone "habitat", środowisko uruchomieniowe kontenera, komputerach bez systemu operacyjnego lub PaaS, nie jest już fokus i nie ogranicza aplikację. 

W tym artykule opisano korzyści z używania platformy Habitat.

## <a name="support-for-the-modern-application"></a>Obsługa aplikacji modern
Pakiety platformy habitat zawierają wszystko, co aplikacja musi zostać uruchomiony przez cały cykl życia. Składniki podstawowe firmy habitat to:
- Format pakowania — aplikacje w pakiecie Habitat są niepodzielne, niezmienne i inspekcji.
- Pakiety aplikacji w Habitat nadzorca jest uruchamiany przy użyciu rozpoznawania elementów równorzędnych te pakiety relacje, strategii uaktualniania i zasad zabezpieczeń. Nadzorca Habitat konfiguruje i zarządza aplikacji dla dowolnego środowiska jest obecny.
- Ekosystem platformy Habitat także przyjmuje planu platformy Habitat kompilacji, który tworzy pakiet Habitat i publikuje go do magazynu usługi kompilacji.

## <a name="run-any-application-anywhere"></a>Uruchamianie dowolnej aplikacji w dowolnym miejscu
Za pomocą systemu Habitat aplikacje mogą być uruchamiane zostały zmodyfikowane w dowolnym środowisku uruchomieniowym. Aplikacja może być cokolwiek z komputerów bez systemu operacyjnego i maszyn wirtualnych do kontenerów (takich jak platforma Docker), systemów zarządzania klastrami (na przykład Mesosphere lub Kubernetes) i systemy PaaS (takie jak Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Łatwo portu starszych aplikacji
Gdy starsze aplikacje zostaną opakowane w pakiecie Habitat, aplikacje są niezależne od środowiska, dla której zostały pierwotnie zaprojektowane. Pakietów można szybko przenosić do bardziej nowoczesnych środowiskach chmury lub kontenerów. Ponadto ponieważ pakiety platformy Habitat standardowe i na zewnątrz interfejs umożliwiający dostęp do Internetu, starsze aplikacje stają się znacznie łatwiejsze w zarządzaniu.

## <a name="improve-the-container-experience"></a>Ulepszenia kontenera
Habitat zmniejsza złożoność zarządzania kontenerami w środowiskach produkcyjnych. Dzięki automatyzacji konfiguracji aplikacji w kontenerze, Habitat adresów twarzy deweloperów trudności podczas przenoszenia aplikacji opartych na kontenerach z środowisk deweloperskich w środowisku produkcyjnym.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrowanie przepływ pracy DevOps programu Chef
Projekt platformy Habitat to sponsor Chef. Habitat wykorzystuje doświadczenie związane z programem Chef z automatyzację infrastruktury, aby udostępnić możliwości automatyzacji niezrównaną na potrzeby aplikacji. Program chef będzie oferty komercyjnej pomocy technicznej dla platformy Habitat i zapewnić bezproblemową integrację między Habitat i dostarczanie Chef, aby zautomatyzować cykl wersji aplikacji, od projektowania do wdrożenia.

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz maszynę wirtualną Windows na platformie Azure przy użyciu programu Chef](/azure/virtual-machines/windows/chef-automation)