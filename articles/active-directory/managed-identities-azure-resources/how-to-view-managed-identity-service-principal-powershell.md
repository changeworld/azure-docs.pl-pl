---
title: Sposób wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu programu PowerShell
description: Szczegółowe instrukcje dotyczące wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu programu PowerShell.
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
ms.date: 11/29/2018
ms.author: priyamo
ms.openlocfilehash: bb0462820cc94c06054ada12f0c764d4cfb190f7
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900157"
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


