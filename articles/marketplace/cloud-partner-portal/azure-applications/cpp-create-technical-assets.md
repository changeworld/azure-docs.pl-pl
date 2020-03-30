---
title: Tworzenie zasobów technicznych aplikacji platformy Azure | Azure Marketplace
description: Tworzenie zasobów technicznych dla oferty aplikacji platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285269"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Przygotowywanie zasobów technicznych aplikacji platformy Azure

W tym artykule opisano zasoby dotyczące przygotowywania zasobów technicznych dla oferty aplikacji platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj poniższy klip wideo, [Szablony rozwiązań do tworzenia i aplikacje zarządzane dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), omówienie sposobu tworzenia szablonu usługi Azure Resource Manager w celu zdefiniowania rozwiązania aplikacji platformy Azure, a następnie publikowanie oferty aplikacji w portalu Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Zapoznaj się z następującą dokumentacją aplikacji platformy Azure, która zawiera przewodniki Szybki start, samouczki i przykłady.

- [Opis szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Przewodniki Szybki start:

  - [Szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/)
  - [Szablony przewodnika Szybki start platformy GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publikowanie definicji aplikacji](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Wdrażanie aplikacji katalogu usług](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Samouczki:

  - [Tworzenie plików definicji](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publish marketplace application (Publikowanie aplikacji w witrynie Marketplace)](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Przykłady:

    - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Rozwiązania aplikacji zarządzanych](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty.

Zespół inżynierów powinien posiadać wiedzę na temat następujących technologii firmy Microsoft:

- Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/)
- Jak [projektować i projektować aplikacje platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage)i sieci platformy [Azure](https://azure.microsoft.com/services/?filter=networking)
- Wiedza robocza na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Wiedza robocza [json](https://www.json.org/)

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz jedno lub oba z następujących środowisk skryptów, aby ułatwić zarządzanie aplikacją platformy Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Zalecamy dodanie następujących narzędzi do środowiska programistycznego:

- [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Kod programu Visual Studio](https://code.visualstudio.com/) z następującymi rozszerzeniami:

  - Rozszerzenie: [narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozszerzenie: [Upiększyć](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozszerzenie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerujemy również przejrzenie dostępnych narzędzi na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/) oraz, jeśli używasz programu Visual Studio, portalu Visual Studio [Marketplace.](https://marketplace.visualstudio.com/)

## <a name="next-steps"></a>Następne kroki

[Tworzenie oferty aplikacji platformy Azure](./cpp-create-offer.md)

