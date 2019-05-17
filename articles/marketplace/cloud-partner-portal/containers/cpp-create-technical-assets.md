---
title: Tworzenie obrazu kontenerów platformy Azure, zasoby techniczne | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla kontenera platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794341"
---
# <a name="prepare-your-container-technical-assets"></a>Przygotowanie kontenera zasoby techniczne

W tym artykule opisano kroki i wymagania dotyczące konfigurowania kontenera oferty w portalu Azure Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przegląd [usługi Azure Container Instances](https://docs.microsoft.com/azure/container-instances) dokumentacji, który zapewnia szybki Start, samouczków i przykładów.

## <a name="fundamental-technical-knowledge"></a>Podstawową wiedzę techniczną

Projektowania, tworzenia i testowania te zasoby potrwać i wymaga wiedzę techniczną dotyczącą platformy Azure i technologii umożliwiających tworzenie oferty.
 
Oprócz domenę rozwiązania zespołowi inżynierów powinien mieć wiedzy na następujących technologiach firmy Microsoft:

-   Podstawową wiedzę na temat [usług platformy Azure](https://azure.microsoft.com/services/) 
-   Jak [projektowania i architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
-   Praktyczną wiedzę na temat [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/), [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
-   Praktyczną wiedzę na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Praktyczną wiedzę na temat [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz jedną lub obie następujące środowiska skryptów ułatwiających zarządzanie obraz kontenera:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Ponadto zaleca się dodanie następujących narzędzi do swojego środowiska projektowego:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Numer wewnętrzny: [Narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Numer wewnętrzny: [Upiększanie](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Numer wewnętrzny: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Także podpowiedzieć dostępne narzędzia w [narzędzi deweloperskich platformy Azure](https://azure.microsoft.com/tools/) strony i, jeśli używasz programu Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Zapoznaj się z poniższymi, aby uzyskać więcej informacji:

* [Samouczek: Tworzenie obrazu kontenera do wdrożenia w usłudze Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Samouczek: Tworzenie i wdrażanie obrazów kontenerów w chmurze za pomocą zadań rejestru kontenera platformy Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie oferty usługi kontenera](./cpp-create-offer.md)
