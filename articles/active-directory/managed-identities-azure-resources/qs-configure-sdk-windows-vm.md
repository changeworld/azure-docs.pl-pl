---
title: Konfigurowanie tożsamości zarządzanych na maszynie Wirtualnej — Azure AD za pomocą zestawu SDK
description: Instrukcje krok po kroku dotyczące konfigurowania i używania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu zestawu SDK platformy Azure.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183480"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurowanie maszyny Wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure przy użyciu zestawu SDK platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewnia usługom platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure Active Directory (AD). Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć tożsamości zarządzane dla zasobów platformy Azure dla maszyny Wirtualnej platformy Azure przy użyciu narzędzia Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>ZestawY SDK platformy Azure z zarządzanymi tożsamościami dla obsługi zasobów platformy Azure 

Platforma Azure obsługuje wiele platform programowania za pośrednictwem serii zestawów [SDK platformy Azure.](https://azure.microsoft.com/downloads) Kilka z nich zostało zaktualizowanych do obsługi zarządzanych tożsamości dla zasobów platformy Azure i zapewniają odpowiednie przykłady, aby zademonstrować użycie. Ta lista jest aktualizowana w miarę dodawania dodatkowej pomocy technicznej:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Zarządzanie zasobami z maszyny wirtualnej włączonej z włączoną tożsamościami zarządzanymi dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Zarządzanie magazynem z maszyny Wirtualnej włączonej przy obliczu zarządzanych tożsamości zasobów platformy Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Tworzenie maszyny Wirtualnej z włączoną tożsamością zarządzaną przypisaną do systemu](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Tworzenie maszyny Wirtualnej z włączoną tożsamością zarządzaną przypisaną do systemu](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Tworzenie maszyny wirtualnej platformy Azure z włączoną tożsamością przypisaną do systemu](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Następne kroki

- Zobacz artykuły pokrewne w obszarze **Konfigurowanie tożsamości dla maszyny Wirtualnej platformy Azure**, aby dowiedzieć się, jak można również korzystać z witryny Azure portal, PowerShell, interfejsu wiersza polecenia i szablonów zasobów.
