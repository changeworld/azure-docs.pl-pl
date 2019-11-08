---
title: Tworzenie zasobów technicznych aplikacji platformy Azure | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla oferty aplikacji platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827607"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Przygotowywanie zasobów technicznych aplikacji platformy Azure

W tym artykule opisano zasoby dotyczące przygotowania zasobów technicznych dla oferty aplikacji platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zapoznaj się z poniższym wideo, [tworzeniem szablonów rozwiązań i zarządzanymi aplikacjami dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)— omówienie sposobu tworzenia szablonu Azure Resource Manager w celu zdefiniowania rozwiązania aplikacji platformy Azure, a następnie sposobu opublikowania Oferta aplikacji w witrynie Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Zapoznaj się z poniższą dokumentacją dotyczącą aplikacji platformy Azure, która zawiera Przewodniki Szybki Start, samouczki i przykłady.

- [Informacje o szablonach Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Przewodniki Szybki start:

  - [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/)
  - [Szablony szybkiego startu platformy Azure w witrynie GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publikowanie definicji aplikacji](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Wdróż aplikację katalogu usług](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Samouczki:

  - [Utwórz pliki definicji](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publish marketplace application (Publikowanie aplikacji w witrynie Marketplace)](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Badan

    - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Zarządzane rozwiązania aplikacji](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, kompilowanie i testowanie tych zasobów jest czasochłonne i wymaga znajomości technicznej platformy Azure i technologii używanych do tworzenia oferty.

Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

- Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/)
- [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
- Wiedza o pracy [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Wiedza o pracy w formacie [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz co najmniej jedno z następujących środowisk skryptów, aby ułatwić zarządzanie aplikacją platformy Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Zalecamy dodanie następujących narzędzi do środowiska deweloperskiego:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) z następującymi rozszerzeniami:

  - Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerujemy również przeglądanie dostępnych narzędzi na stronie [usługi Azure narzędzia deweloperskie](https://azure.microsoft.com/tools/) i, jeśli używasz programu Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Następne kroki

[Tworzenie oferty aplikacji platformy Azure](./cpp-create-offer.md)

