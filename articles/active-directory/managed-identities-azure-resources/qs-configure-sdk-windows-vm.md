---
title: Użyj zestawu SDK platformy Azure, aby skonfigurować Maszynę wirtualną z zarządzanych tożsamości dla zasobów platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania i korzystania z zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure, przy użyciu zestawu Azure SDK.
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
ms.date: 09/28/2017
ms.author: priyamo
ms.openlocfilehash: 7d95600323e862513c1856a5015f400e2ae814eb
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887244"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurowanie maszyny Wirtualnej z zarządzanych tożsamości dla zasobów platformy Azure przy użyciu zestawu Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory (AD). Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i Usuń zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure, przy użyciu zestawu Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Zestawy SDK platformy Azure z zarządzanych tożsamości dla zasobów platformy Azure obsługują 

Platforma Azure obsługuje wiele platform programistycznych w kolejnych seriach [zestawami SDK Azure](https://azure.microsoft.com/downloads). Niektóre z nich zostały zaktualizowane do obsługi tożsamości zarządzanej dla zasobów platformy Azure i podaj odpowiednie przykłady, aby zademonstrować użycie. Ta lista jest aktualizowana po dodaniu dodatkowej pomocy technicznej:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Zarządzanie zasobami z maszyny Wirtualnej włączone z zarządzanych tożsamości dla zasobów platformy Azure, włączone](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Zarządzanie magazynem za pomocą maszyny Wirtualnej z systemem włączone z zarządzanych tożsamości dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Tworzenie maszyny Wirtualnej za pomocą przypisany systemowo tożsamość zarządzaną włączone](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Tworzenie maszyny Wirtualnej za pomocą przypisany systemowo tożsamość zarządzaną włączone](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Tworzenie maszyny Wirtualnej platformy Azure przy użyciu tożsamości przypisanych przez system, włączone](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Kolejne kroki

- Zobacz pokrewne artykuły w obszarze **Konfigurowanie tożsamości dla maszyny Wirtualnej platformy Azure**, aby dowiedzieć się, jak również używać szablonów platformy Azure portal, programu PowerShell, interfejsu wiersza polecenia i zasobów.
