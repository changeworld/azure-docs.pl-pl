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
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 1d9dd7d19c196679ead9b552bcf296b4acd4ca68
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842893"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Zmień właściciela subskrypcji użytkownika usługi Azure Stack

Operatorzy usługi Azure Stack można zmienić właściciela rozliczeń subskrypcji użytkownika za pomocą programu PowerShell. Jednym z powodów zmiany właściciela, na przykład, jest zastąpienie użytkownik opuszcza organizację.

Istnieją dwa rodzaje *właścicieli* są przypisane do subskrypcji:

- **Właściciel rozliczeń**: Domyślnie właściciel rozliczeń jest konto użytkownika, który pobiera subskrypcji z oferty, a następnie należy relacja rozliczeń dla tej subskrypcji. To konto jest również administratorem subskrypcji. Tylko jedno konto użytkownika może mieć oznaczenie to w ramach subskrypcji. Właściciel rozliczeń jest często organizacji lub zespołu potencjalnego klienta.

  Można użyć polecenia cmdlet programu PowerShell [AzsUserSubscription zestaw](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) można zmienić właściciela rozliczeń.  

- **Właściciele dodane za pośrednictwem ról RBAC** — dodatkowym użytkownikom można udzielić **właściciela** przy użyciu roli [kontroli dostępu opartej na rolach](azure-stack-manage-permissions.md) systemu (RBAC). Dowolna liczba dodatkowych kont użytkowników można dodawać jako właścicieli do uzupełniania rozliczeń właściciela. Dodatkowych właścicieli są Administratorzy subskrypcji i mają wszystkie uprawnienia dla subskrypcji, z wyjątkiem uprawnienia do usunięcia właściciela rozliczeń.

  Za pomocą programu PowerShell do zarządzania dodatkowych właścicieli. Więcej informacji znajduje się w [tym artykule](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Zmień właściciela rozliczeń

Uruchom następujący skrypt, aby zmienić właściciela rozliczeń subskrypcji użytkownika. Komputera, którego używasz do uruchamiania skryptu musi nawiązać połączenie z usługi Azure Stack i uruchom moduł usługi Azure Stack PowerShell 1.3.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie programu Azure Stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>W wielu dzierżawy usługi Azure Stack nowy właściciel musi być w tym samym katalogu co istniejące właściciela. Przed zapewnieniem własności subskrypcji do użytkownika, który znajduje się w innym katalogu, należy najpierw [zaprosić użytkownika jako gościa do katalogu](../active-directory/b2b/add-users-administrator.md).

Zastąp następujące wartości w skrypcie, zanim zostanie ona uruchomiona:

- **$ArmEndpoint**: Punkt końcowy usługi Resource Manager dla danego środowiska.
- **$TenantId**: Twoim identyfikatorem dzierżawy.
- **$SubscriptionId**: Identyfikator subskrypcji.
- **$OwnerUpn**: Konto, na przykład **użytkownika\@example.com**, aby dodać jako nowy właściciel rozliczeń.

```powershell
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

- [Zarządzanie kontrolą dostępu opartej na rolach](azure-stack-manage-permissions.md)
