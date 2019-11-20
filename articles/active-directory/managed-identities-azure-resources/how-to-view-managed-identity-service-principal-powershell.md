---
title: Wyświetlanie jednostki usługi zarządzanej tożsamości przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania nazwy głównej usługi zarządzanej tożsamości przy użyciu programu PowerShell.
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
ms.openlocfilehash: 9ac2d1d4fc5e669f63bff49a7948db74ec1baa62
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184129"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Wyświetlanie jednostki usługi zarządzanej tożsamości przy użyciu programu PowerShell

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [aplikacji](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Zainstalowanie najnowszej wersji programu [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Następujące polecenie demonstruje sposób wyświetlania nazwy głównej usługi maszyny wirtualnej lub aplikacji z włączoną tożsamością przypisanej do systemu. Zastąp `<VM or application name>` własnymi wartościami.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o wyświetlaniu nazw podmiotów usługi Azure AD przy użyciu programu PowerShell, zobacz [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


