---
title: Sposób wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu programu PowerShell
description: Szczegółowe instrukcje dotyczące wyświetlania nazwy głównej usługi z tożsamości zarządzanej przy użyciu programu PowerShell.
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
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0ad3a52b837a5f79c9976c4c509e0a8516de1e7d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714119"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Wyświetl jednostki usługi z tożsamości zarządzanej przy użyciu programu PowerShell

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić jednostki usługi z tożsamości zarządzanej przy użyciu programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamości przypisanej w systemie na maszynie wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) lub [aplikacji](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Jeśli używasz programu PowerShell lokalnie, wykonaj również te czynności: 
    - Uruchom polecenie `Login-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.
    - Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Uruchom polecenie `Install-Module -Name PowerShellGet -AllowPrerelease`, aby pobrać wersję wstępną modułu `PowerShellGet` (po uruchomieniu tego polecenia może być konieczne uruchomienie polecenia `Exit` umożliwiającego zakończenie bieżącej sesji programu PowerShell w celu zainstalowania modułu `AzureRM.ManagedServiceIdentity`).
    - Uruchom `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` do zainstalowania wstępnej wersji `AzureRM.ManagedServiceIdentity` modułu, aby wykonać użytkownik przypisany zarządzanych tożsamości operacje, w tym artykule.

## <a name="view-the-service-principal"></a>Wyświetl nazwy głównej usługi

Następujące polecenie pokazuje sposób wyświetlania nazwy głównej usługi, maszyny Wirtualnej lub aplikacji przy użyciu tożsamości przypisanej w systemie włączony. Zastąp `<VM or application name>` własnymi wartościami.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o wyświetlaniu jednostki usługi Azure AD przy użyciu programu PowerShell, zobacz [Get AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


