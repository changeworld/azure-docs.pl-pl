---
title: Tworzenie aplikacji platformy Azure, zasoby techniczne | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla oferty aplikacji platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: cbe1b8c8f1159d90fbf97eeae272c1c50ec9b9bb
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942987"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Przygotowanie zasobów technicznych aplikacji platformy Azure

W tym artykule opisano zasoby do przygotowania zasoby techniczne oferty aplikacji platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj następujący film [tworzenia szablonów rozwiązań, a Managed Applications dla witryny Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), omówienie sposobu tworzenia szablonu usługi Azure Resource Manager do definiowania rozwiązania aplikacji platformy Azure i następnie sposobu następnie należy opublikować ofertę aplikacji w portalu Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Przejrzyj następująca dokumentacja aplikacji Azure zawiera Przewodniki Szybki Start, samouczków i przykładów.

- [Omówienie szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Przewodniki Szybki start:

  - [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/)
  - [Szablony szybkiego startu platformy Azure w witrynie GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publikowanie definicji aplikacji](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Wdróż aplikację katalogu usług](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Samouczki:

  - [Tworzenie plików definicji](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publish marketplace application (Publikowanie aplikacji w witrynie Marketplace)](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Przykłady:

    - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Rozwiązania aplikacji zarządzanych](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Podstawową wiedzę techniczną

Projektowania, tworzenia i testowania te zasoby potrwać i wymaga wiedzę techniczną dotyczącą platformy Azure i technologii umożliwiających tworzenie oferty.

Twój zespół inżynieryjny powinien mieć wiedzy na temat następujących technologii firmy Microsoft:

- Podstawową wiedzę na temat [usług platformy Azure](https://azure.microsoft.com/services/)
- Jak [projektowania i architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktyczną wiedzę na temat [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/), [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage), i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
- Praktyczną wiedzę na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktyczną wiedzę na temat [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz jedną lub obie następujące środowiska skryptów pomagające w zarządzaniu aplikacji platformy Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Zaleca się dodanie następujących narzędzi do swojego środowiska projektowego:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) z następującymi rozszerzeniami:

  - Numer wewnętrzny: [Narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Numer wewnętrzny: [Upiększanie](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Numer wewnętrzny: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Także podpowiedzieć dostępne narzędzia w [narzędzi deweloperskich platformy Azure](https://azure.microsoft.com/tools/) strony i, jeśli używasz programu Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie oferty aplikacji platformy Azure](./cpp-create-offer.md)

