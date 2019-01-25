---
title: Jak używać zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure z zestawami SDK Azure
description: Przykłady dotyczące używania zestawów SDK usługi Azure za pomocą maszyny Wirtualnej platformy Azure zarządza tożsamości dla zasobów platformy Azure kodu.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.openlocfilehash: 0d6d122e7f05a14344292fe84608c384c5dcc306
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882059"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Jak używać zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure z zestawami SDK Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera listę przykładowych zestawach SDK, które pokazują użycie ich odpowiednich SDK Azure pomocy technicznej dla zarządzanych tożsamości dla zasobów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Wszystkie próbki kodu lub skryptu w tym artykule przyjęto założenie, klient jest uruchomiony na maszynie Wirtualnej z zarządzanych tożsamości dla zasobów platformy Azure są włączone. Funkcja VM "Połącz" w witrynie Azure portal na połączenie zdalne z maszyną wirtualną. Aby uzyskać więcej informacji na temat włączania zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md), lub jeden z tych artykułów variant (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub platformy Azure ZESTAW SDK). 

## <a name="sdk-code-samples"></a>Przykładowy kod zestawu SDK

| SDK             | Przykład kodu |
| --------------- | ----------- |
| .NET            | [Wdrażanie szablonu usługi Azure Resource Manager z maszyny Wirtualnej Windows przy użyciu zarządzanych tożsamości dla zasobów platformy Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Wywoływanie usług platformy Azure z maszyny Wirtualnej systemu Linux przy użyciu zarządzanych tożsamości dla zasobów platformy Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Zarządzanie zasobami przy użyciu zarządzanych tożsamości dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Użyj zarządzanych tożsamości dla zasobów platformy Azure, aby uwierzytelnić po prostu z wewnątrz maszyny Wirtualnej](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Zarządzanie zasobami z poziomu maszyny Wirtualnej z zarządzanych tożsamości dla zasobów platformy Azure, włączone](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [zestawami SDK Azure](https://azure.microsoft.com/downloads/) pełną listę zasobów zestawu SDK platformy Azure, łącznie z biblioteki pliki do pobrania, dokumentację i inne.
- Aby włączyć zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md).








