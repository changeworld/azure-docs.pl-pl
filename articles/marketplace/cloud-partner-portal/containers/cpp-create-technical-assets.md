---
title: Tworzenie zasobów technicznych obrazów kontenerów platformy Azure | Azure Marketplace
description: Tworzenie zasobów technicznych dla kontenera platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280069"
---
# <a name="prepare-your-container-technical-assets"></a>Przygotowywanie zasobów technicznych kontenera

W tym artykule opisano kroki i wymagania dotyczące konfigurowania oferty kontenera w portalu Azure Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj dokumentację [wystąpień kontenerów platformy Azure,](https://docs.microsoft.com/azure/container-instances) która zawiera przewodniki Szybki start, samouczki i przykłady.

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty.
 
Oprócz domeny rozwiązania zespół inżynierów powinien posiadać wiedzę na temat następujących technologii firmy Microsoft:

-   Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/) 
-   Jak [projektować i projektować aplikacje platformy Azure](https://azure.microsoft.com/solutions/architecture/)
-   Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i sieci [azure](https://azure.microsoft.com/services/?filter=networking)
-   Wiedza robocza na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Wiedza robocza [json](https://www.json.org/)

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz jedno lub oba z następujących środowisk skryptów, aby ułatwić zarządzanie obrazem kontenera:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Ponadto zaleca się dodanie następujących narzędzi do środowiska programistycznego:

-   [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Kod programu Visual Studio](https://code.visualstudio.com/)
    *   Rozszerzenie: [narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Rozszerzenie: [Upiększyć](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Rozszerzenie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerujemy również przejrzenie dostępnych narzędzi na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/) oraz, jeśli używasz programu Visual Studio, portalu Visual Studio [Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Aby uzyskać więcej informacji, zobacz następujące informacje:

* [Samouczek: Tworzenie obrazu kontenera do wdrożenia w wystąpieniach kontenera platformy Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Samouczek: Tworzenie i wdrażanie obrazów kontenerów w chmurze za pomocą zadań rejestru kontenerów platformy Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Następne kroki

[Tworzenie oferty kontenerów](./cpp-create-offer.md)
