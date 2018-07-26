---
title: Konfigurowanie tożsamości usługi zarządzanej włączono maszyny Wirtualnej platformy Azure przy użyciu zestawu Azure SDK
description: Krok po kroku instrukcje dotyczące konfigurowania i używania tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure, przy użyciu zestawu Azure SDK.
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 2763c78d309f5a90d68429caa46581e50f8b4303
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257664"
---
# <a name="configure-a-vm-managed-service-identity-using-an-azure-sdk"></a>Konfigurowanie tożsamości usługi VM-Managed przy użyciu zestawu Azure SDK

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory (AD). Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć tożsamości usługi zarządzanej dla maszyny Wirtualnej platformy Azure, przy użyciu zestawu Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-service-identity-support"></a>Zestawy SDK platformy Azure z obsługą tożsamości usługi zarządzanej 

Platforma Azure obsługuje wiele platform programistycznych w kolejnych seriach [zestawami SDK Azure](https://azure.microsoft.com/downloads). Niektóre z nich zostały zaktualizowane do obsługi tożsamości usługi zarządzanej i podaj odpowiednie przykłady, aby zademonstrować użycie. Ta lista jest aktualizowana po dodaniu dodatkowej pomocy technicznej:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Zarządzanie zasobami z włączoną tożsamości usługi Zarządzanej maszyny Wirtualnej](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Zarządzanie magazynem z włączoną tożsamości usługi Zarządzanej maszyny Wirtualnej](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Utwórz Maszynę wirtualną z włączoną tożsamością usługi Zarządzanej](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Utwórz Maszynę wirtualną z włączoną tożsamością usługi Zarządzanej](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Tworzenie maszyny Wirtualnej platformy Azure przy użyciu pliku MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Kolejne kroki

- Zobacz powiązane artykuły w obszarze "Konfigurowanie zarządzanych usługi tożsamości dla maszyny Wirtualnej platformy Azure", aby dowiedzieć się, jak również używać szablonów platformy Azure portal, programu PowerShell, interfejsu wiersza polecenia i zasobów.

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
