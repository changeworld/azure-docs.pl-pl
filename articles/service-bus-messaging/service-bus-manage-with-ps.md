---
title: Zarządzanie zasobami usługi Azure Service Bus przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie zasobami usługi Service Bus przy użyciu modułu programu PowerShell
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 0d15aa4d7b8a922f7606b7c4d1b357a80b3cbfab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311050"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Zarządzanie zasobami usługi Service Bus przy użyciu programu PowerShell

Microsoft Azure PowerShell to środowisko skryptów, w którym można użyć do kontrolowania i zautomatyzować wdrożenie i zarządzanie usługami platformy Azure. W tym artykule opisano sposób używania [modułu programu PowerShell usługi Service Bus Resource Manager](/powershell/module/az.servicebus) aprowizować i zarządzać jednostkami usługi Service Bus (przestrzenie nazw, kolejki, tematy i subskrypcje) przy użyciu lokalnej konsoli programu Azure PowerShell lub skryptów.

Można również zarządzać jednostkami usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz artykuł [zasobów Utwórz usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania niezbędne są następujące wymagania wstępne:

* Subskrypcja platformy Azure. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [opcje zakupu][purchase options], [oferty dla subskrybentów][member offers], lub [bezpłatne konto][free account].
* Komputer z programem Azure PowerShell. Aby uzyskać instrukcje, zobacz [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/get-started-azureps).
* Ogólna wiedza o skryptów programu PowerShell, pakietów NuGet i .NET Framework.

## <a name="get-started"></a>Rozpoczęcie pracy

Pierwszym krokiem jest Zaloguj się do Twojego konta platformy Azure i subskrypcję platformy Azure przy użyciu programu PowerShell. Postępuj zgodnie z instrukcjami w [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/get-started-azureps) Zaloguj się do konta platformy Azure i pobrać i uzyskiwać dostęp do zasobów w subskrypcji platformy Azure.

## <a name="provision-a-service-bus-namespace"></a>Aprowizacja przestrzeni nazw usługi Service Bus

Podczas pracy z przestrzeni nazw usługi Service Bus, można użyć [Get AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [AzServiceBusNamespace Usuń](/powershell/module/az.servicebus/remove-azservicebusnamespace)i [ Zestaw AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) polecenia cmdlet.

W tym przykładzie tworzy kilka zmiennych lokalnych w skrypcie; `$Namespace` i `$Location`.

* `$Namespace` jest nazwą przestrzeni nazw usługi Service Bus za pomocą którego chcemy pracować.
* `$Location` identyfikuje centrum danych, w którym firma Microsoft aprowizacja przestrzeni nazw.
* `$CurrentNamespace` przechowuje odwołanie do przestrzeni nazw, który możemy pobrać (lub Utwórz).

W skrypcie rzeczywiste `$Namespace` i `$Location` mogą być przekazywane jako parametry.

Ta część skrypt wykonuje następujące czynności:

1. Próbuje pobrać przestrzeni nazw usługi Service Bus przy użyciu określonej nazwy.
2. Przestrzeń nazw zostanie znaleziony, raporty, co został znaleziony.
3. Jeśli przestrzeń nazw nie zostanie znaleziony, tworzy przestrzeń nazw, a następnie pobiera nowo utworzoną przestrzeń nazw.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Utwórz regułę autoryzacji przestrzeni nazw

Poniższy przykład pokazuje, jak zarządzać przy użyciu reguły autoryzacji przestrzeni nazw [New AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [ Zestaw AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule), i [AzServiceBusAuthorizationRule Usuń](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) polecenia cmdlet.

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Aby utworzyć kolejki lub tematu, przeprowadź sprawdzanie przestrzeni nazw przy użyciu skryptu w poprzedniej sekcji. Następnie utwórz kolejkę:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modyfikowanie właściwości kolejki

Po wykonaniu skryptu w poprzedniej sekcji, można użyć [AzServiceBusQueue zestaw](/powershell/module/az.servicebus/set-azservicebusqueue) polecenia cmdlet, aby zaktualizować właściwości kolejki, jak w poniższym przykładzie:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Inicjowanie obsługi administracyjnej innych jednostek usługi Service Bus

Możesz użyć [modułu programu PowerShell usługi Service Bus](/powershell/module/az.servicebus) do aprowizowania innych jednostek, takich jak tematy i subskrypcje. Te polecenia cmdlet są składniowo podobne do polecenia cmdlet tworzenia kolejki, które zostały przedstawione w poprzedniej sekcji.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz pełną dokumentację modułu programu PowerShell usługi Service Bus Resource Manager [tutaj](/powershell/module/az.servicebus). Ta strona zawiera listę wszystkich dostępnych poleceń cmdlet.
- Aby dowiedzieć się, jak za pomocą szablonów usługi Azure Resource Manager, zobacz artykuł [zasobów Utwórz usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informacje o [biblioteki zarządzania usługi Service Bus .NET](service-bus-management-libraries.md).

Istnieją pewne alternatywne sposoby zarządzać jednostkami usługi Service Bus, zgodnie z opisem w tych wpisów w blogu:

* [Tworzenie kolejek, tematów i subskrypcji przy użyciu skryptu programu PowerShell usługi Service Bus](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Jak utworzyć Namespace magistrali usług i Centrum zdarzeń za pomocą skryptu programu PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Skrypty programu PowerShell usługi Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
