---
title: Wyświetlanie jednostki usługi tożsamości zarządzanej przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania jednostki usługi tożsamości zarządzanej przy użyciu programu PowerShell.
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
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33fc6fbfd7c47b5809e8aff8ee9806c9eeac1162
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298686"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Wyświetlanie jednostki usługi tożsamości zarządzanej przy użyciu programu PowerShell

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostkę usługi tożsamości zarządzanej przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną do systemu na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Zainstalowanie najnowszej wersji programu [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Wyświetl jednostkę usługi

To następujące polecenie pokazuje, jak wyświetlić jednostkę usługi maszyny Wirtualnej lub aplikacji z włączoną tożsamością przypisaną przez system. Zamień `<VM or application name>` na własne wartości.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wyświetlania podmiotów usługi Azure AD przy użyciu programu PowerShell, zobacz [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


