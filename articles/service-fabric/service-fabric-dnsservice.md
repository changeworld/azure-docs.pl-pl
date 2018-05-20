---
title: Usługa Azure Service Fabric DNS | Dokumentacja firmy Microsoft
description: Za pomocą usługi dns platformy Service Fabric wykrywania mikrousług z wewnątrz klastra.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="dns-service-in-azure-service-fabric"></a>Usługa DNS w sieci szkieletowej usług Azure
Usługa DNS jest opcjonalny systemu usługi można włączyć w klastrze do odnajdywania innych usług za pomocą protokołu DNS. 

Wiele usług, zwłaszcza konteneryzowanych usług, może mieć nazwę istniejącego adresu URL, a możliwość rozwiązać je przy użyciu standardowego protokołu DNS (a nie protokołu Naming Service) jest pożądane, szczególnie w scenariuszach "przyrostu i przesunięcia". Usługa DNS umożliwia mapowanie nazwy DNS na nazwę usługi i dlatego rozpoznać adresów IP punktu końcowego. 

Usługa DNS mapuje nazwy DNS nazwy usługi, które z kolei są rozpoznawane przez usługę nazewnictwa do zwracania punktu końcowego usługi. Nazwy DNS dla usługi znajduje się w momencie tworzenia obiektu.

![Punkty końcowe usługi](./media/service-fabric-dnsservice/dns.png)

Porty dynamiczne nie są obsługiwane przez usługę DNS. Aby rozwiązać narażone na porty dynamiczne usługi, użyj [wstecznego usługi serwera proxy](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Włączanie usługi DNS
Podczas tworzenia klastra przy użyciu portalu usługi DNS jest domyślnie włączone w **usługi DNS obejmują** pole wyboru na **konfiguracji klastra** menu:

![Włączanie usługi DNS za pośrednictwem portalu][2]

Jeśli nie używasz portalu do tworzenia klastra lub aktualizujesz istniejącego klastra, należy włączyć usługę DNS w szablonie:

- Aby wdrożyć nowy klaster, można użyć [przykładowy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) lub utworzyć własny szablon Menedżera zasobów. 
- Aby zaktualizować istniejącego klastra, można przejść do grupy zasobów klastra w portalu, a następnie kliknij **skryptu automatyzacji** do pracy z szablonu, która odzwierciedla bieżący stan klastra i innych zasobów w grupie. Aby dowiedzieć się więcej, zobacz [wyeksportować szablon z grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Po utworzeniu szablonu można włączyć usługę DNS z następujących kroków:

1. Sprawdź, czy `apiversion` ustawiono `2017-07-01-preview` lub nowszy `Microsoft.ServiceFabric/clusters` zasobów i, jeśli nie, zaktualizować go jak pokazano w poniższy fragment kodu:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz włączyć usługę DNS przez dodanie poniższego `addonFeatures` sekcji po `fabricSettings` sekcji, jak pokazano w poniższy fragment kodu: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Po aktualizacji szablonu klastra z poprzednim zmiany ich zastosowania i umożliwić Uaktualnianie ukończone. Po zakończeniu uaktualniania usługi system DNS uruchamiania w klastrze. Nazwa usługi jest `fabric:/System/DnsService`, a inne można znaleźć go w **systemu** usługi sekcji w narzędziu Service Fabric explorer. 


## <a name="setting-the-dns-name-for-your-service"></a>Ustawienie nazwy DNS dla usługi
Gdy usługa DNS działa w klastrze, można ustawić nazwy DNS dla usługi deklaratywnie dla usług domyślnych w `ApplicationManifest.xml` lub za pomocą poleceń programu PowerShell.

Nazwy DNS dla usługi jest rozpoznawalna w całym klastrze. Zdecydowanie zaleca się, że używasz schemat nazewnictwa `<ServiceDnsName>.<AppInstanceName>`, na przykład `service1.application1`. W ten sposób zapewnia unikatowość nazwy DNS w całym klastrze. Jeśli aplikacja jest wdrażana przy użyciu rozwiązania Docker compose, usługi są automatycznie przypisywane nazwy DNS przy użyciu ten schemat nazewnictwa.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ustawienie nazwy DNS dla usługi domyślne w pliku ApplicationManifest.xml
Otwórz projekt w Visual Studio lub ulubionego edytora, a `ApplicationManifest.xml` pliku. Przejdź do sekcji domyślnej usługi i dla każdej usługi, dodać `ServiceDnsName` atrybutu. Poniższy przykład pokazuje, jak ustawić nazwę DNS usługi `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Gdy aplikacja jest wdrażana, wystąpienie usługi w programie Service Fabric explorer zawiera nazwę DNS dla tego wystąpienia, jak pokazano na poniższej ilustracji: 

![Punkty końcowe usługi][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ustawienie nazwy DNS dla usługi przy użyciu programu Powershell
Można ustawić nazwy DNS dla usługi, tworząc go za pomocą `New-ServiceFabricService` środowiska Powershell. Poniższy przykład tworzy nową usługę bezstanowych z nazwą DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Przy użyciu systemu DNS w usługach
Jeśli wdrożono więcej niż jedna usługa, można znaleźć punktów końcowych z innymi usługami w celu komunikowania się z przy użyciu nazwy DNS. Usługa DNS ma zastosowanie tylko do usług bezstanowych, ponieważ protokół DNS nie może komunikować się z usługami stanowych. Dla stanowych usług, można użyć wbudowanej [wstecznego usługi serwera proxy](./service-fabric-reverseproxy.md) dla połączeń http do wywołania partycji określonej usługi. Porty dynamiczne nie są obsługiwane przez usługę DNS. Można użyć zwrotnego serwera proxy do rozpoznania usług, które używają portów dynamicznych.

Poniższy kod przedstawia sposób wywołania innej usługi, która to po prostu wywołanie regularne http, w którym podać port i dowolną ścieżkę opcjonalne jako część adresu URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat usługi komunikacji w klastrze z [połączenia i łączyć się z usługami](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
