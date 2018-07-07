---
title: Jak używać usługi Azure VM tożsamości usługi zarządzanej z zestawami SDK Azure
description: Przykłady kodu dla przy użyciu zestawów SDK platformy Azure przy użyciu tożsamości usługi Zarządzanej maszyny Wirtualnej platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 1c7a0d10f8ff005d90bb77f33bf40a00f97e078f
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901758"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Jak używać usługi Azure VM tożsamość usługi zarządzanej (MSI) z zestawami SDK Azure 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera listę przykładowych zestawach SDK, które pokazują użycie ich odpowiednich SDK Azure, obsługę tożsamości usługi Zarządzanej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Wszystkie próbki kodu lub skryptu w tym artykule przyjęto założenie, klient jest uruchomiony na maszynie wirtualnej włączona MSI. Funkcja VM "Połącz" w witrynie Azure portal na połączenie zdalne z maszyną wirtualną. Aby uzyskać więcej informacji na temat włączania MSI na maszynie Wirtualnej, zobacz [Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md), lub jeden z tych artykułów variant (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu SDK platformy Azure). 

## <a name="sdk-code-samples"></a>Przykładowy kod zestawu SDK

| SDK             | Przykład kodu |
| --------------- | ----------- |
| .NET            | [Wdrażanie szablonu usługi Azure Resource Manager z maszyny Wirtualnej Windows przy użyciu tożsamości usługi zarządzanej](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Wywoływanie usług platformy Azure z maszyny Wirtualnej systemu Linux przy użyciu tożsamości usługi zarządzanej](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Zarządzanie zasobami za pomocą tożsamości usługi zarządzanej](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Uwierzytelnianie po prostu z wewnątrz maszyny Wirtualnej przy użyciu pliku MSI](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Zarządzanie zasobami z włączoną tożsamości usługi Zarządzanej maszyny Wirtualnej](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Powiązana zawartość

- Zobacz [zestawami SDK Azure](https://azure.microsoft.com/downloads/) pełną listę zasobów zestawu SDK platformy Azure, łącznie z biblioteki pliki do pobrania, dokumentację i inne.
- Aby włączyć tożsamości usługi Zarządzanej maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.








