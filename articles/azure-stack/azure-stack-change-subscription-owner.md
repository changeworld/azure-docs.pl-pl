---
title: Zaktualizować właściciela subskrypcji użytkownika usługi Azure Stack | Dokumentacja firmy Microsoft
description: Zmień właściciela rozliczeń dla subskrypcji użytkownika usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: 46dd9c786fddb2f6ce9fb8a761a1b9f135a82d74
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959200"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Zmień właściciela subskrypcji użytkownika usługi Azure Stack

Operatorzy usługi Azure Stack można zmienić właściciela rozliczeń subskrypcji użytkownika za pomocą programu PowerShell. Jednym z powodów zmiany właściciela, na przykład, jest zastąpienie użytkownik opuszcza organizację.   

Istnieją dwa rodzaje *właścicieli* są przypisane do subskrypcji:

- **Właściciel rozliczeń**: domyślnie rozliczeń właściciel jest konto użytkownika, który pobiera subskrypcji z oferty, a następnie należy relacja rozliczeń dla tej subskrypcji. To konto jest również administratorem subskrypcji. Tylko jedno konto użytkownika może mieć oznaczenie to w ramach subskrypcji. Właściciel rozliczeń jest często organizacji lub zespołu potencjalnego klienta. 

  Możesz użyć polecenia cmdlet programu PowerShell [AzsUserSubscription zestaw](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) można zmienić właściciela rozliczeń.  

- **Właściciele dodane za pośrednictwem ról RBAC** — dodatkowym użytkownikom można udzielić **właściciela** przy użyciu roli [kontroli dostępu opartej na rolach](azure-stack-manage-permissions.md) systemu (RBAC). Dowolna liczba dodatkowych kont użytkowników można dodawać jako właścicieli do uzupełniania rozliczeń właściciela. Dodatkowych właścicieli są Administratorzy subskrypcji i mają wszystkie uprawnienia dla subskrypcji, z wyjątkiem uprawnienia do usunięcia właściciela rozliczeń. 

  Można użyć programu PowerShell, zarządzenie właścicielami dodatkowych, zobacz [programu Azure PowerShell do zarządzania kontrolą dostępu opartą na rolach](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Zmień właściciela rozliczeń

Uruchom następujący skrypt, aby zmienić właściciela rozliczeń subskrypcji użytkownika. Komputera, którego używasz do uruchamiania skryptu musi nawiązać połączenie z usługi Azure Stack i uruchom moduł usługi Azure Stack PowerShell 1.3.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie programu Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]
>  W wielu dzierżawy usługi Azure Stack nowy właściciel musi być w tym samym katalogu co istniejące właściciela. Przed zapewnieniem własności subskrypcji do użytkownika, który znajduje się w innym katalogu, należy najpierw [zaprosić użytkownika jako gościa do katalogu](../active-directory/b2b/add-users-administrator.md). 

Zastąp następujące wartości w skrypcie, zanim zostanie ona uruchomiona: 
 
- **$ArmEndpoint**: Określ punkt końcowy usługi Resource Manager dla danego środowiska.  
- **$TenantId**: Określ identyfikatora dzierżawy. 
- **$SubscriptionId**: Określ identyfikator subskrypcji.
- **$OwnerUpn**: Określ konto jako **user@example.com** do dodania jako nowy właściciel rozliczeń.  

```PowerShell   
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie kontrolą dostępu opartej na rolach](azure-stack-manage-permissions.md)
