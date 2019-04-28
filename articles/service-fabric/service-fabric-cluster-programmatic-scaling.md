---
title: Azure Service Fabric programowe skalowania | Dokumentacja firmy Microsoft
description: Skalowanie klastra usługi Azure Service Fabric lub programowo, zgodnie z niestandardowych wyzwalaczy
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
ms.openlocfilehash: 552c9820cca4380c00e1bf435fdb3d068c0690fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111307"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Programowe skalowania klastra usługi Service Fabric 

Klastry usługi Service Fabric działających na platformie Azure są tworzone na podstawie zestawów skalowania maszyn wirtualnych.  [Skalowanie klastra](./service-fabric-cluster-scale-up-down.md) w tym artykule opisano, jak klastry usługi Service Fabric można skalować ręcznie lub za pomocą reguł automatycznego skalowania. W tym artykule opisano, jak Zarządzanie poświadczeniami i skalować w klastrze na platformie lub się przy użyciu fluent Azure compute zestaw SDK, który jest bardziej zaawansowanym scenariuszu. Aby uzyskać przegląd, przeczytaj [metody programowe koordynacji operacji skalowania Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Zarządzanie poświadczeniami
Jednym z wyzwań pisania usługi do obsługi skalowania jest, czy usługa musi umożliwiać dostęp do zasobów zestawu skalowania maszyn wirtualnych bez logowania interakcyjnego. Uzyskiwanie dostępu do klastra usługi Service Fabric jest łatwe, jeśli usługa skalowania modyfikuje własną aplikację usługi Service Fabric, ale poświadczenia są wymagane do dostępu do zestawu skalowania. Aby zalogować się, można użyć [nazwy głównej usługi](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) utworzone za pomocą [wiersza polecenia platformy Azure](https://github.com/azure/azure-cli).

Nazwy głównej usługi można utworzyć wykonując następujące kroki:

1. Zaloguj się do wiersza polecenia platformy Azure (`az login`) jako użytkownik z uprawnieniami do skalowania maszyn wirtualnych zestawu
2. Tworzenie jednostki za pomocą usługi `az ad sp create-for-rbac`
    1. Zanotuj identyfikator aplikacji (nazywane "identyfikator klienta" w innym miejscu), nazwy, hasła i dzierżawy w celu późniejszego użycia.
    2. Należy również Identyfikatora subskrypcji, które można przeglądać za pomocą `az account list`

Biblioteki fluent obliczeń może zalogować się przy użyciu tych poświadczeń w następujący sposób (należy pamiętać, że podstawowych fluent typów platformy Azure, takich jak `IAzure` znajdują się w [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) pakietu):

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

Po zalogowaniu, liczba wystąpień zestawu skalowania można wykonywać zapytania za pomocą `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Skalowanie w poziomie
Za pomocą fluent Azure compute zestawu SDK, wystąpienia mogą być dodawane do maszyny wirtualnej zestawu skalowania przy użyciu zaledwie kilku wywołań-

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternatywnie rozmiar zestawu skalowania maszyn wirtualnych, można też zarządzać przy użyciu poleceń cmdlet programu PowerShell. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) można pobrać obiektu zestawu skalowania maszyn wirtualnych. Bieżąca pojemność jest dostępna za pośrednictwem `.sku.capacity` właściwości. Po zmianie pojemność na żądaną wartość, można zaktualizować zestawu na platformie Azure skalowania maszyn wirtualnych za pomocą [ `Update-AzVmss` ](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) polecenia.

Podczas ręcznego dodawania węzła, dodając wystąpienia zestawu skalowania powinny być wszystko, co jest potrzebne do rozpoczęcia nowego węzła usługi Service Fabric, ponieważ szablon zestawu skalowania obejmuje rozszerzenia automatycznie dołączy nowe wystąpienia w klastrze usługi Service Fabric. 

## <a name="scaling-in"></a>Skalowanie w

Skalowanie w jest podobny do skalowania w poziomie. Rzeczywiste zestawu skalowania maszyn wirtualnych zmiany są praktycznie takie same. Jednak ponieważ została omówiony wcześniej, usługi Service Fabric tylko automatycznie oczyszcza usuniętych węzłów z trwałością Gold i Silver. Dlatego w trwałości brązowa skalowania w przypadku jest niezbędne do interakcji z klastrem usługi Service Fabric, aby zamknąć węzeł, który ma zostać usunięty a następnie usunąć jego stan.

Przygotowanie węzła dla zamykania polega na znajdowaniu węzeł, który ma zostać usunięty (ostatnio dodane maszyny wirtualnej wystąpienia w zestawie skalowania) i dezaktywowanie go. Wystąpienia zestawu skalowania maszyn wirtualnych są numerowane w kolejności, w której są dodawane, więc nowszej węzłów można znaleźć, porównując sufiks liczbowy w nazwach węzłów (które dopasowanie podstawowego zestawu skalowania maszyn wirtualnych nazwy wystąpień). 

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

Po znalezieniu węzeł, który ma zostać usunięty, można dezaktywować i usunąć, korzystając z tych samych `FabricClient` wystąpienia i `IAzure` wystąpienie z wcześniej.

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

Jako przy użyciu skalowania w poziomie, poleceń cmdlet programu PowerShell do modyfikowania skalowania maszyn wirtualnych pojemności zestawu można również w tym miejscu Jeśli skryptów podejście jest preferowane. Po usunięciu wystąpienia maszyny wirtualnej można usunąć stanu węzła usługi Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć implementowanie własnej logiki automatyczne skalowanie, zapoznać się z następujących pojęć i interfejsy API przydatne:

- [Skalowanie ręcznie lub za pomocą reguł skalowania automatycznego](./service-fabric-cluster-scale-up-down.md)
- [Fluent bibliotek zarządzania platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (przydatne w przypadku interakcji z podstawowych zestawów skalowania maszyn wirtualnych w klastrze usługi Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (przydatne w przypadku interakcji z klastrem usługi Service Fabric i jego węzły)
