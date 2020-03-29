---
title: Zarządzanie zasobami usługi Azure Service Bus za pomocą programu PowerShell | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak używać modułu programu Azure PowerShell do tworzenia i zarządzania encjami usługi Service Bus (przestrzenie nazw, kolejki, tematy, subskrypcje).
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e333dfb109840538fd5dec8110e1c32adedce989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759265"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Zarządzanie zasobami usługi Service Bus za pomocą programu PowerShell

Microsoft Azure PowerShell to środowisko skryptów, którego można używać do kontrolowania i automatyzacji wdrażania i zarządzania usługami platformy Azure. W tym artykule opisano, jak używać [modułu Programu PowerShell menedżera zasobów usługi Service Bus](/powershell/module/az.servicebus) do aprowizowania i zarządzania encjami usługi Service Bus (przestrzeniami nazw, kolejkami, tematami i subskrypcjami) przy użyciu lokalnej konsoli lub skryptu programu Azure PowerShell.

Można również zarządzać jednostkami usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem potrzebne są następujące wymagania wstępne:

* Subskrypcja platformy Azure. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [opcje zakupu,][purchase options] [oferty członkowskie][member offers]lub [bezpłatne konto.][free account]
* Komputer z programem Azure PowerShell. Aby uzyskać instrukcje, zobacz [Wprowadzenie do poleceń cmdlet programu Azure PowerShell.](/powershell/azure/get-started-azureps)
* Ogólne zrozumienie skryptów programu PowerShell, pakietów NuGet i programu .NET Framework.

## <a name="get-started"></a>Wprowadzenie

Pierwszym krokiem jest użycie programu PowerShell do logowania się do konta platformy Azure i subskrypcji platformy Azure. Postępuj zgodnie z instrukcjami w [Wprowadzenie do poleceń cmdlet programu Azure PowerShell,](/powershell/azure/get-started-azureps) aby zalogować się do konta platformy Azure i pobrać i uzyskać dostęp do zasobów w ramach subskrypcji platformy Azure.

## <a name="provision-a-service-bus-namespace"></a>Aprowizuj obszar nazw usługi Service Bus

Podczas pracy z obszarami nazw magistrali usług można użyć poleceń cmdlet [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)i [Set-AzServiceBusNamespace.](/powershell/module/az.servicebus/set-azservicebusnamespace)

W tym przykładzie tworzy kilka zmiennych lokalnych w skrypcie; `$Namespace` i `$Location`.

* `$Namespace`to nazwa obszaru nazw usługi Service Bus, z którą chcemy pracować.
* `$Location`identyfikuje centrum danych, w którym aprowizujemy obszar nazw.
* `$CurrentNamespace`przechowuje obszar nazw odwołań, który pobieramy (lub tworzymy).

W rzeczywistym skrypcie `$Namespace` i `$Location` mogą być przekazywane jako parametry.

Ta część skryptu wykonuje następujące czynności:

1. Próbuje pobrać obszar nazw usługi Service Bus o określonej nazwie.
2. Jeśli obszar nazw zostanie znaleziony, raportuje to, co zostało znalezione.
3. Jeśli obszar nazw nie zostanie znaleziony, tworzy obszar nazw, a następnie pobiera nowo utworzony obszar nazw.
   
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

### <a name="create-a-namespace-authorization-rule"></a>Tworzenie reguły autoryzacji obszaru nazw

W poniższym przykładzie pokazano, jak zarządzać regułami autoryzacji obszaru nazw przy użyciu polecenia cmdlets [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule).

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

Aby utworzyć kolejkę lub temat, należy sprawdzić obszar nazw przy użyciu skryptu w poprzedniej sekcji. Następnie utwórz kolejkę:

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

Po wykonaniu skryptu w poprzedniej sekcji można użyć polecenia cmdlet [Set-AzServiceBusQueue,](/powershell/module/az.servicebus/set-azservicebusqueue) aby zaktualizować właściwości kolejki, jak w poniższym przykładzie:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Inicjowanie obsługi administracyjnej innych jednostek usługi Service Bus

Moduł [PowerShell usługi Service Bus](/powershell/module/az.servicebus) służy do aprowizowania innych jednostek, takich jak tematy i subskrypcje. Te polecenia cmdlet są syntaktycznie podobne do poleceń cmdlet tworzenia kolejki zademonstrowanych w poprzedniej sekcji.

## <a name="next-steps"></a>Następne kroki

- Zobacz pełną dokumentację modułu programu PowerShell menedżera zasobów usługi Service Bus [tutaj](/powershell/module/az.servicebus). Ta strona zawiera listę wszystkich dostępnych poleceń cmdlet.
- Aby uzyskać informacje dotyczące korzystania z szablonów usługi Azure Resource Manager, zobacz artykuł [Tworzenie zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informacje o [bibliotekach zarządzania usługą Service Bus .NET](service-bus-management-libraries.md).

Istnieje kilka alternatywnych sposobów zarządzania jednostkami usługi Service Bus, zgodnie z opisem w tych wpisach w blogu:

* [Jak utworzyć kolejki, tematy i subskrypcje usługi Service Bus przy użyciu skryptu programu PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Jak utworzyć obszar nazw magistrali usług i Centrum zdarzeń przy użyciu skryptu programu PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Skrypty programu PowerShell magistrali usług](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
