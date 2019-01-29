---
title: Sposób wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu programu PowerShell
description: Szczegółowe instrukcje dotyczące wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.openlocfilehash: 8c406dda1d6ce0fe7b73f400444d7bcd8a9e8400
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185962"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Wyświetl jednostki usługi z tożsamości zarządzanej przy użyciu programu PowerShell

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostki usługi z tożsamości zarządzanej przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamości przypisanej w systemie na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Zainstaluj najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Wyświetl nazwy głównej usługi

Następujące polecenie pokazuje sposób wyświetlania nazwy głównej usługi, maszyny Wirtualnej lub aplikacji przy użyciu tożsamości przypisanej w systemie włączony. Zastąp `<VM or application name>` własnymi wartościami.

```PowerShell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o wyświetlaniu jednostki usługi Azure AD przy użyciu programu PowerShell, zobacz [Get AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


