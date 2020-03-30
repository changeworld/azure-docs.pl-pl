---
title: Skalowanie programowe sieci szkieletowej usług azure
description: Programowo skalowanie klastra sieci szkieletowej usług Azure, zgodnie z niestandardowymi wyzwalaczami
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458285"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Programowo skaluj klaster sieci szkieletowej usług 

Klastry sieci szkieletowej usług uruchomione na platformie Azure są tworzone na podstawie zestawów skalowania maszyn wirtualnych.  [Skalowanie klastra](./service-fabric-cluster-scale-up-down.md) opisuje, jak klastry sieci szkieletowej usług mogą być skalowane ręcznie lub za pomocą reguł skalowania automatycznego. W tym artykule opisano sposób zarządzania poświadczeniami i skalowania klastra w lub na zewnątrz przy użyciu płynnego zestawu SDK obliczeń platformy Azure, który jest bardziej zaawansowanym scenariuszem. Aby uzyskać omówienie, przeczytaj [programowe metody koordynowania operacji skalowania platformy Azure.](service-fabric-cluster-scaling.md#programmatic-scaling) 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Zarządzanie poświadczeniami
Jednym z wyzwań związanych z pisaniem usługi do obsługi skalowania jest to, że usługa musi mieć dostęp do zasobów zestawu skalowania maszyny wirtualnej bez logowania interakcyjnego. Dostęp do klastra sieci szkieletowej usług jest łatwe, jeśli usługa skalowania modyfikuje własną aplikację sieci szkieletowej usług, ale poświadczenia są potrzebne do uzyskania dostępu do zestawu skalowania. Aby się zalogować, można użyć [jednostki usługi utworzonej](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) za pomocą [interfejsu wiersza polecenia platformy Azure.](https://github.com/azure/azure-cli)

Jednostki usługi można utworzyć za pomocą następujących kroków:

1. Zaloguj się do interfejsu`az login`wiersza polecenia platformy Azure ( ) jako użytkownik z dostępem do zestawu skalowania maszyny wirtualnej
2. Utwórz jednostkę usługi za pomocą`az ad sp create-for-rbac`
    1. Zanotuj identyfikator appId (nazywany "identyfikatorem klienta" w innym miejscu), nazwę, hasło i dzierżawę do późniejszego użycia.
    2. Potrzebny będzie również identyfikator subskrypcji, który można przeglądać za pomocą`az account list`

Płynna biblioteka obliczeniowa może logować się przy użyciu tych poświadczeń w następujący sposób (należy pamiętać, że podstawowe typy platformy Azure, takie jak `IAzure` pakiet [Microsoft.Azure.Management.Fluent):](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)

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

Po zalogowaniu się liczba wystąpień zestawu `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`skalowania może być wyszukiwana za pośrednictwem pliku .

## <a name="scaling-out"></a>Skalowanie w poziomie
Przy użyciu płynnego zestawu SDK obliczeń platformy Azure wystąpienia można dodać do zestawu skalowania maszyny wirtualnej za pomocą zaledwie kilku wywołań -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternatywnie rozmiar zestawu skalowania maszyny wirtualnej można również zarządzać za pomocą poleceń cmdlet programu PowerShell. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)można pobrać obiekt zestawu skalowania maszyny wirtualnej. Aktualna pojemność dostępna `.sku.capacity` jest za pośrednictwem obiektu. Po zmianie pojemności na żądaną wartość skala maszyny wirtualnej [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) ustawiona na platformie Azure może zostać zaktualizowana za pomocą polecenia.

Podobnie jak podczas ręcznego dodawania węzła, dodawanie wystąpienia zestawu skalowania powinno być wszystkim, co jest potrzebne do uruchomienia nowego węzła sieci szkieletowej usług, ponieważ szablon zestawu skalowania zawiera rozszerzenia, aby automatycznie łączyć nowe wystąpienia z klastrem sieci szkieletowej usług. 

## <a name="scaling-in"></a>Skalowanie w

Skalowanie w jest podobny do skalowania w poziomie. Rzeczywiste zmiany zestawu skalowania maszyny wirtualnej są praktycznie takie same. Ale, jak wspomniano wcześniej, sieci szkieletowej tylko automatycznie czyści usunięte węzły z trwałością złota lub srebra. Tak w skali brązowa trwałość w przypadku, konieczne jest do interakcji z klastra sieci szkieletowej usług, aby zamknąć węzeł do usunięcia, a następnie usunąć jego stan.

Przygotowanie węzła do zamknięcia obejmuje znalezienie węzła do usunięcia (ostatnio dodane wystąpienie zestawu skalowania maszyny wirtualnej) i dezaktywację go. Wystąpienia zestawu skalowania maszyny wirtualnej są numerowane w kolejności, w jakiej są dodawane, więc nowsze węzły można znaleźć, porównując sufiks liczb w nazwach węzłów (które odpowiadają podstawowym nazwom wystąpień zestawu skalowania maszyny wirtualnej). 

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

Po znalezieniu węzła do usunięcia można go dezaktywować i usunąć przy użyciu tego samego `FabricClient` wystąpienia i `IAzure` wystąpienia z wcześniejszego.

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

Podobnie jak w przypadku skalowania w poziomie, polecenia cmdlet programu PowerShell do modyfikowania pojemności zestawu skalowania maszyny wirtualnej mogą być również używane w tym miejscu, jeśli preferowane jest podejście skryptów. Po usunięciu wystąpienia maszyny wirtualnej można usunąć stan węzła sieci szkieletowej usługi.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć wdrażanie własnej logiki automatycznego skalowania, zapoznaj się z następującymi pojęciami i przydatnymi interfejsami API:

- [Skalowanie ręcznie lub za pomocą reguł skalowania automatycznego](./service-fabric-cluster-scale-up-down.md)
- [Płynne biblioteki zarządzania platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (przydatne do interakcji z podstawowymi zestawami skalowania maszyny wirtualnej klastra sieci szkieletowej usług)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (przydatne do interakcji z klastrem sieci szkieletowej usług i jego węzłów)
