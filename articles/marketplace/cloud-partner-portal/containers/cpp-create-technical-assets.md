---
title: Utwórz zasoby techniczne obrazów kontenerów platformy Azure | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla kontenera platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819045"
---
# <a name="prepare-your-container-technical-assets"></a>Przygotuj zasoby techniczne kontenera

W tym artykule opisano kroki i wymagania dotyczące konfigurowania kontenera oferowanego w witrynie Azure Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zapoznaj się z dokumentacją [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) , która zawiera Przewodniki Szybki Start, samouczki i przykłady.

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, kompilowanie i testowanie tych zasobów jest czasochłonne i wymaga znajomości technicznej platformy Azure i technologii używanych do tworzenia oferty.
 
Poza swoją domeną rozwiązania Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

-   Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/) 
-   [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
-   Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
-   Wiedza o pracy [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Wiedza o pracy w formacie [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz co najmniej jedno z następujących środowisk skryptów, aby ułatwić zarządzanie obrazem kontenera:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Ponadto zalecamy Dodawanie następujących narzędzi do środowiska deweloperskiego:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerujemy również przeglądanie dostępnych narzędzi na stronie [usługi Azure narzędzia deweloperskie](https://azure.microsoft.com/tools/) i, jeśli używasz programu Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Aby uzyskać więcej informacji, zobacz następujące informacje:

* [Samouczek: Tworzenie obrazu kontenera na potrzeby wdrożenia Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Samouczek: kompilowanie i wdrażanie obrazów kontenerów w chmurze za pomocą zadań Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Następne kroki

[Tworzenie oferty kontenera](./cpp-create-offer.md)
