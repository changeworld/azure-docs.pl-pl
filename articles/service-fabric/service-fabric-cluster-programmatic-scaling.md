---
title: Azure Usługa sieci szkieletowej programowe skalowanie | Dokumentacja firmy Microsoft
description: Skalowanie klastra usługi sieć szkieletowa usług Azure lub programistycznie, zgodnie z wyzwalaczy niestandardowych
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: dcf4721012fb8ec39bcd1de02c294747357b3539
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Programowo skalowanie klastra sieci szkieletowej usług 

Klastrów sieci szkieletowej usług działających na platformie Azure są wbudowane zestawy skalowania maszyny wirtualnej.  [Skalowanie klastra](./service-fabric-cluster-scale-up-down.md) w tym artykule opisano, jak mogą być skalowane klastrów sieci szkieletowej usług ręcznie lub przy użyciu reguł automatycznego skalowania. W tym artykule opisano sposób zarządzania poświadczeniami i skalowania klastra w lub wychodzących za pomocą fluent Azure obliczeniowe zestaw SDK, który jest bardziej zaawansowanym scenariuszu. Aby uzyskać ogólne informacje, przeczytaj [metod programistycznych koordynacji Azure skalowanie operacji](service-fabric-cluster-scaling.md#programmatic-scaling). 

## <a name="manage-credentials"></a>Zarządzanie poświadczeniami
Jednym z wyzwań zapisywania usługi do obsługi skalowania jest, że usługi musi być możliwy dostęp do zasobów zestawu skali maszyny wirtualnej bez logowania interakcyjnego. Uzyskiwanie dostępu do klastra usługi sieć szkieletowa jest proste, jeśli skalowania usługi modyfikuje własnej aplikacji usługi Service Fabric, ale poświadczenia są wymagane do zestawu skalowania. Aby zalogować się, można użyć [nazwy głównej usługi](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) utworzone za pomocą [Azure CLI 2.0](https://github.com/azure/azure-cli).

Nazwy głównej usługi mogą być tworzone z następujących kroków:

1. Zaloguj się do wiersza polecenia platformy Azure (`az login`) jako użytkownik z dostępem do skalowania maszyny wirtualnej ustawić
2. Tworzenie nazwy głównej z usługi `az ad sp create-for-rbac`
    1. Zanotuj identyfikator aplikacji (nazywane "identyfikator klienta" w innym miejscu), nazwa, hasło i dzierżawy w celu późniejszego użycia.
    2. Należy również identyfikator subskrypcji można wyświetlić w programie `az account list`

Biblioteka fluent obliczeń zalogować się przy użyciu tych poświadczeń w następujący sposób (należy pamiętać, że fluent Azure typów podstawowych, takich jak `IAzure` w [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) pakietu):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Po zalogowaniu, liczba wystąpień zestawu skali można tworzyć zapytania za pomocą `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Skalowanie w poziomie
Przy użyciu fluent Azure obliczeniowe zestawu SDK, wystąpień, mogą być dodawane do zestaw z kilku wywołania - skalowania maszyny wirtualnej

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternatywnie rozmiaru zestawu skali maszyny wirtualnej, można też zarządzać za pomocą poleceń cmdlet programu PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) można pobrać obiektu zestawu skali maszyny wirtualnej. Pojemność bieżąca jest dostępna za pośrednictwem `.sku.capacity` właściwości. Po zmianie pojemność na żądaną wartość, skalowania maszyny wirtualnej w usłudze Azure można zaktualizować za pomocą [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) polecenia.

Podczas dodawania węzła ręcznie, dodając wystąpienia zestawu skalowania powinny być wszystkie punkty, które ma potrzebne do uruchomienia nowego węzła sieci szkieletowej usług, ponieważ szablon zestawu skalowania obejmują rozszerzenia automatycznie dołączy do klastra usługi sieć szkieletowa nowych wystąpień. 

## <a name="scaling-in"></a>Skalowanie w

Skalowanie w jest podobny do skalowania. Rzeczywiste zestawu skalowania maszyn wirtualnych zmiany praktycznie są takie same. Jednak jak został już wcześniej, usługa sieć szkieletowa tylko automatycznie oczyszcza usuniętych węzłów trwałości Gold lub Silver. Tak, w tym trwałości brązowa skalowania w przypadku należy interakcje z klastrem usługi sieć szkieletowa można zamknąć węzeł ma zostać usunięty, a następnie usunąć jego stanu.

Przygotowanie węzła dla zamknięcia polega na znajdowaniu węzeł, który ma być usunięty (ostatnio dodane wystąpienie maszyny wirtualnej skali zestawu) i dezaktywowanie go. Wystąpienia zestawu skali maszyny wirtualnej są numerowane w kolejności, są one dodawane, więc węzłów nowszej można znaleźć na podstawie porównania ilości numer sufiks nazwy tych węzłów (które dopasowania podstawowej zestawu skalowania maszyn wirtualnych nazw wystąpień). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Po znalezieniu węzeł ma zostać usunięty, można dezaktywować i usunąć korzystającej z tego samego `FabricClient` wystąpienia i `IAzure` wystąpienie z wcześniej.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Jako ze skalowania poleceń cmdlet programu PowerShell modyfikowania skalowania maszyny wirtualnej zestawu pojemności można także tutaj Jeśli skryptów podejście jest bardziej pożądane. Po usunięciu wystąpienie maszyny wirtualnej, można usunąć stan węzła sieci szkieletowej usług.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć wdrażanie własną logikę automatyczne skalowanie, należy zapoznać się z przydatne interfejsów API i następujące kwestie:

- [Skalowanie ręcznie lub przy użyciu reguł automatycznego skalowania](./service-fabric-cluster-scale-up-down.md)
- [Fluent biblioteki zarządzania platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (przydatne w przypadku interakcji z podstawowej zestawy skalowania maszyny wirtualnej w klastrze usługi sieć szkieletowa)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (przydatne w przypadku interakcji z klastra sieci szkieletowej usług i jego węzły)
