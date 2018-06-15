---
title: Za pomocą programu PowerShell do zarządzania zasobami Azure Service Bus | Dokumentacja firmy Microsoft
description: Moduł PowerShell umożliwia tworzenie i zarządzanie zasobami usługi Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4fbc6e18565ec14a3ccb4499b24804f681026023
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159742"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Zarządzanie zasobami usługi Service Bus przy użyciu programu PowerShell

Microsoft Azure PowerShell jest środowiskiem skryptów, które umożliwia kontrolowanie i automatyzowania wdrażania i zarządzania usługami Azure. W tym artykule opisano sposób użycia [modułu programu PowerShell dla Menedżera zasobów usługi magistrali](/powershell/module/azurerm.servicebus) obsługiwać i zarządzać nim jednostek usługi Service Bus (przestrzeni nazw, kolejki, tematy i subskrypcje) przy użyciu konsoli programu Azure PowerShell lokalnej lub skryptu.

Można również zarządzać jednostek usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz artykuł [zasobów usługi Service Bus utworzyć przy użyciu szablonów usługi Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, potrzebne są następujące wymagania wstępne:

* Subskrypcja platformy Azure. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [opcji zakupu][purchase options], [oferty Członkowskie][member offers], lub [bezpłatne konto][free account].
* Komputer z programem Azure PowerShell. Aby uzyskać instrukcje, zobacz [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/get-started-azureps).
* Ogólny opis skryptów programu PowerShell, pakietami NuGet i .NET Framework.

## <a name="get-started"></a>Rozpoczęcie pracy

Pierwszym krokiem jest zalogować się do swojego konta platformy Azure i subskrypcji platformy Azure przy użyciu programu PowerShell. Postępuj zgodnie z instrukcjami [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/get-started-azureps) Zaloguj się do konta platformy Azure i pobierania i uzyskać dostęp do zasobów w Twojej subskrypcji platformy Azure.

## <a name="provision-a-service-bus-namespace"></a>Zainicjuj obsługę przestrzeni nazw usługi Service Bus

Podczas pracy z przestrzeni nazw usługi Service Bus, można użyć [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [AzureRmServiceBusNamespace nowy](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), i [AzureRmServiceBusNamespace zestaw](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) polecenia cmdlet.

W tym przykładzie powoduje utworzenie kilku zmiennych lokalnych w skrypcie; `$Namespace` i `$Location`.

* `$Namespace`to nazwa przestrzeni nazw usługi Service Bus, z którą chcemy pracy.
* `$Location`Określa centrum danych, w którym możemy udostępnić przestrzeni nazw.
* `$CurrentNamespace`przechowuje przestrzeń nazw odwołania, którego możemy pobrać (lub Utwórz).

W skrypcie rzeczywiste `$Namespace` i `$Location` mogą być przekazywane jako parametry.

Ta część skrypt wykonuje następujące czynności:

1. Próbuje pobrać przestrzeni nazw usługi Service Bus przy użyciu określonej nazwy.
2. Przestrzeń nazw zostanie znaleziony, raporty, co znaleziono.
3. Jeśli przestrzeń nazw nie zostanie znaleziony, tworzy przestrzeń nazw, a następnie pobiera nowo utworzonej przestrzeni nazw.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Utwórz regułę autoryzacji przestrzeni nazw

Poniższy przykład przedstawia sposób zarządzać przy użyciu reguł autoryzacji przestrzeni nazw [AzureRmServiceBusNamespaceAuthorizationRule nowy](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [AzureRmServiceBusNamespaceAuthorizationRule zestaw](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), i [polecenia cmdlet Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Aby utworzyć kolejka lub temat, wykonaj sprawdzenie przestrzeni nazw przy użyciu skryptu w poprzedniej sekcji. Następnie utwórz kolejkę:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modyfikowanie właściwości kolejki

Po wykonaniu skryptu w poprzedniej sekcji, można użyć [AzureRmServiceBusQueue zestaw](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) polecenia cmdlet, aby zaktualizować właściwości kolejki, jak w poniższym przykładzie:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Inicjowanie obsługi innych jednostek usługi Service Bus

Można użyć [modułu programu PowerShell usługi Service Bus](/powershell/module/azurerm.servicebus) do udostępnienia inne jednostki, na przykład tematów i subskrypcji. Te polecenia cmdlet są składniowo podobne do polecenia cmdlet tworzenia kolejki zostało to pokazane w poprzedniej sekcji.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz pełną dokumentację modułu programu PowerShell Menedżera zasobów usługi magistrali [tutaj](/powershell/module/azurerm.servicebus). Ta strona zawiera listę wszystkich dostępnych poleceń cmdlet.
- Aby uzyskać informacje dotyczące korzystania z szablonów usługi Azure Resource Manager, zobacz artykuł [zasobów usługi Service Bus utworzyć przy użyciu szablonów usługi Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informacje o [biblioteki zarządzania .NET magistrali usługi](service-bus-management-libraries.md).

Istnieją pewne alternatywne sposoby zarządzania jednostek usługi Service Bus, zgodnie z opisem w wpisy na blogu:

* [Tworzenie usługi Service Bus kolejki, tematy i subskrypcje, za pomocą skryptu programu PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Jak utworzyć Namespace magistrali usługi i Centrum zdarzeń za pomocą skryptu programu PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Skrypty programu PowerShell usługi Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
