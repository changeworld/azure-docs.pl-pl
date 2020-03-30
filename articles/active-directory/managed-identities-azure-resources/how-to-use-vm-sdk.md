---
title: Używanie tożsamości zarządzanych na maszynie wirtualnej platformy Azure przy użyciu zestawów SDK platformy Azure — usługa Azure AD
description: Przykłady kodu do korzystania z zestawów SDK platformy Azure z maszyną wirtualną platformy Azure, która zarządza tożsamościami zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184148"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu zestawów SDK platformy Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera listę przykładów zestawów SDK, które pokazują użycie ich obsługi odpowiednich zestawów SDK platformy Azure dla tożsamości zarządzanych dla zasobów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Cały przykładowy kod/skrypt w tym artykule zakłada, że klient jest uruchomiony na maszynie Wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure włączonych. Użyj funkcji maszyny Wirtualnej "Połącz" w witrynie Azure portal, aby zdalnie połączyć się z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure](qs-configure-portal-windows-vm.md)lub jednego z artykułów wariantu (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu SDK platformy Azure). 

## <a name="sdk-code-samples"></a>Przykłady kodu SDK

| SDK             | Przykład kodu |
| --------------- | ----------- |
| .NET            | [Wdrażanie szablonu usługi Azure Resource Manager z maszyny Wirtualnej systemu Windows przy użyciu tożsamości zarządzanych dla zasobów platformy Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Wywoływanie usług platformy Azure z maszyny Wirtualnej systemu Linux przy użyciu zarządzanych tożsamości zasobów platformy Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Zarządzanie zasobami przy użyciu tożsamości zarządzanych dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Używanie tożsamości zarządzanych dla zasobów platformy Azure do uwierzytelniania po prostu z wnętrza maszyny Wirtualnej](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Zarządzanie zasobami z maszyny Wirtualnej z włączoną tożsamościami zarządzanymi dla zasobów platformy Azure](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Następne kroki

- Zobacz [zestawy SDK platformy Azure, aby](https://azure.microsoft.com/downloads/) uzyskać pełną listę zasobów zestawów SDK platformy Azure, w tym pliki do pobrania biblioteki, dokumentację i inne.
- Aby włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure.](qs-configure-portal-windows-vm.md)








