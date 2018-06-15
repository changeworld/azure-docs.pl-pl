---
title: Wdrażanie aplikacji na platformie Azure przy użyciu siedliska
description: Dowiedz się, jak spójnie wdrożyć aplikację na maszynach wirtualnych platformy Azure i kontenerów
keywords: Azure, chef, devops, maszyny wirtualne, omówienie można zautomatyzować, siedliska
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267444"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure przy użyciu siedliska
[Siedliska](https://www.habitat.sh/) jest pierwszy jej rodzaju projekt open source, który oferuje całkowicie nowe podejście do zarządzania aplikacjami. Siedliska sprawia, że aplikacja i automatyzacji jego jednostkę wdrożenia. Gdy aplikacje są ujęte w lekkie "środowisku", środowisko uruchomieniowe kontenera, od zera lub PaaS, nie jest już fokus i ograniczają aplikacji. 

W tym artykule opisano korzyści wynikające ze stosowania siedliska.

## <a name="support-for-the-modern-application"></a>Obsługa aplikacji modern
Pakiety siedliska zawierają wszystkie elementy aplikacji musi być uruchamiane przez cały cykl życia. Siedliska podstawowe składniki są:
- Format opakowania — aplikacje w pakiecie siedliska są atomic niezmienne i podlegającą inspekcji.
- Nadzorca siedliska uruchamia pakiety aplikacji z funkcją rozpoznawanie elementu równorzędnego te pakiety relacje, strategii uaktualniania i zasady zabezpieczeń. Nadzorca siedliska konfiguruje i zarządza aplikacji niezależnie od środowiska jest obecny.
- Ekosystem siedliska udostępnia usługę kompilacji, która przyjmuje plan kompilacji siedliska, tworzy pakiet siedliska i publikuje go do magazynu.

## <a name="run-any-application-anywhere"></a>Uruchamiać aplikacje w dowolnym miejscu
Siedliska umożliwia uruchamianie aplikacji zostały zmodyfikowane w dowolnym środowisku środowiska wykonawczego. Obejmuje on wszystko, od zera i maszyn wirtualnych do kontenerów (na przykład Docker), systemy zarządzania klastrem (na przykład Mesosphere lub Kubernetes) i systemów PaaS (na przykład kluczową Foundry chmury).

## <a name="easily-port-legacy-applications"></a>Łatwo portu starszych aplikacji
W przypadku starszych aplikacji są ujęte w pakiecie siedliska, aplikacje są niezależne od środowiska, dla których pierwotnie zostały zaprojektowane. Pakiety szybko mogą zostać przeniesione do bardziej nowoczesnych środowiskach, takich jak chmury lub kontenery. Ponadto ponieważ pakiety siedliska standard na zewnątrz połączonej interfejsu, starsze aplikacje staną się znacznie ułatwia zarządzanie.

## <a name="improve-the-container-experience"></a>Udoskonalanie kontenera
Siedliska zmniejsza się złożoność zarządzania kontenerami w środowiskach produkcyjnych. Dzięki automatyzacji konfiguracji aplikacji w kontenerze, siedliska adresów kroju deweloperzy wyzwania podczas przenoszenia aplikacji kontenera ze środowisk Programowanie w środowisku produkcyjnym.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrowanie opracowywania oprogramowania Chef przepływu pracy
Projekt siedliska jest sponsor Chef. Siedliska wykorzystuje środowisko głębokiego Chef firmy przy użyciu automatyzacji infrastruktury w celu przełączenia możliwości automatyzacji Niespotykana aplikacji. Chef będzie oferować komercyjnej pomocy technicznej dla siedliska i zapewnić bezproblemową integrację między siedlisko i dostarczania Chef można całkowicie zautomatyzować cyklu wersji aplikacji od projektowania do wdrożenia.

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz maszynę wirtualną systemu Windows na platformie Azure przy użyciu Chef](/azure/virtual-machines/windows/chef-automation)