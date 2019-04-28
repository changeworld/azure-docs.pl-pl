---
title: Usługa Azure Service Fabric DNS | Dokumentacja firmy Microsoft
description: Usługa Service Fabric dns do odnajdywania mikrousługi z w ramach klastra.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: aljo
ms.openlocfilehash: 3b3262eadc732c23000a66f24aaeeed4d9794db0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947660"
---
# <a name="dns-service-in-azure-service-fabric"></a>Usługa DNS w usłudze Azure Service Fabric
Usługa DNS to opcjonalna usługa systemowa, możesz włączyć w klastrze, aby odkrywanie innych usług za pomocą protokołu DNS. 

Wiele usług, zwłaszcza usług konteneryzowanych adresowane za pomocą istniejącego adresu URL. Zapewnienie możliwości rozwiązania z tymi usługami przy użyciu standardowego protokołu DNS, a nie protokołu usługi nazewnictwa Service Fabric, jest pożądane. Usługa DNS umożliwia mapowanie nazw DNS na nazwę usługi i dlatego rozpoznawać adresy IP punktów końcowych. Takie funkcje obsługuje możliwość przenoszenia usług konteneryzowanych na różnych platformach i może być "metodą lift and shift" scenariuszy było prostsze, umożliwiając Użyj istniejącej usługi adresy URL zamiast konieczności ponownego pisania kodu, aby korzystać z usługi nazw. 

Usługa DNS mapuje nazwy DNS nazwy usługi, które z kolei są rozwiązywane przez usługę nazewnictwa do zwrócenia z punktem końcowym usługi. Nazwa DNS usługi znajduje się w czasie tworzenia. Na poniższym diagramie przedstawiono, jak działa usługa DNS w przypadku usług bezstanowych.

![punkty końcowe usługi](./media/service-fabric-dnsservice/stateless-dns.png)

Począwszy od usługi Service Fabric w wersji 6.3 protokołu DNS w sieci szkieletowej usługi ma został rozszerzony o schemat adresowania partycjonowane usług stanowych. Te rozszerzenia umożliwiają rozpoznać adresów IP określonej partycji przy użyciu kombinacji usługi stanowej nazwy DNS i nazwy partycji. Wszystkie trzy schematy partycjonowania są obsługiwane:

- O nazwie partycjonowania
- Zakres, partycjonowanie
- Partycje pojedynczego wystąpienia

Na poniższym diagramie przedstawiono, jak działa usługa DNS dla usług stanowych podzielonym na partycje.

![punkty końcowe usługi stanowej](./media/service-fabric-dnsservice/stateful-dns.png)

Porty dynamiczne nie są obsługiwane przez usługę DNS. Aby rozwiązać usług udostępnianych na porty dynamiczne, należy użyć [odwrotnego serwera proxy usługi](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Włączanie usługi DNS
> [!NOTE]
> Usługi DNS dla usługi Service Fabric nie jest jeszcze obsługiwana w systemie Linux.

Podczas tworzenia klastra przy użyciu portalu usługa DNS jest domyślnie włączone w **usługi DNS obejmują** pole wyboru na **konfiguracji klastra** menu:

![Włączanie usługi DNS przy użyciu portalu](./media/service-fabric-dnsservice/enable-dns-service.png)

Jeśli nie używasz portalu do tworzenia klastra lub Trwa aktualizowanie istniejącego klastra, należy włączyć usługę DNS w szablonie:

- Aby wdrożyć nowy klaster, można użyć [przykładowe szablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) lub Utwórz własny szablon usługi Resource Manager. 
- Aby zaktualizować istniejący klaster, możesz przejść do grupy zasobów klastra w portalu i kliknij przycisk **skrypt automatyzacji** do pracy z szablonu, który reprezentuje bieżący stan klastra i innych zasobów w grupie. Aby dowiedzieć się więcej, zobacz [wyeksportować szablon z grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Po utworzeniu szablonu, należy włączyć usługę DNS wykonując następujące kroki:

1. Sprawdź, czy `apiversion` ustawiono `2017-07-01-preview` lub nowszy dla `Microsoft.ServiceFabric/clusters` zasobu i jeśli nie, zaktualizuj go zgodnie z poniższym przykładem:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz Włącz usługę DNS w jednym z następujących sposobów:

   - Aby włączyć usługę DNS przy użyciu ustawień domyślnych, należy dodać go do `addonFeatures` sekcji wewnątrz `properties` jak pokazano w poniższym przykładzie:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - Aby włączyć usługę przy użyciu innych niż domyślne ustawienia, należy dodać `DnsService` sekcji `fabricSettings` sekcji wewnątrz `properties` sekcji. W takim przypadku nie trzeba dodawać usługa DNS do `addonFeatures`. Aby dowiedzieć się więcej na temat właściwości, które można ustawić dla usługi DNS, zobacz [ustawień usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. Po szablonu klastra zostały zaktualizowane, wraz ze zmianami, stosować je i pozwolić uaktualnienia ukończone. Po zakończeniu uaktualniania, usługa system DNS uruchamiania w klastrze. Nazwa usługi jest `fabric:/System/DnsService`, znaleźć go w folderze **systemu** usługi tematu usługi Service Fabric explorer. 


## <a name="setting-the-dns-name-for-your-service"></a>Ustawianie nazwy DNS dla usługi
Można ustawić nazwy DNS dla usług deklaratywne dla usług domyślnych w pliku ApplicationManifest.xml lub za pomocą poleceń programu PowerShell.

Nazwa DNS usługi jest rozpoznawany w całym klastrze, więc jest ważne zapewnić unikatowość nazwy DNS w klastrze. 

Zdecydowanie zaleca się, że używasz schemat nazewnictwa `<ServiceDnsName>.<AppInstanceName>`, na przykład `service1.application1`. Jeśli aplikacja jest wdrażana za pomocą platformy Docker compose, usług są automatycznie przypisywane nazwy DNS przy użyciu ten schemat nazewnictwa.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ustawianie nazwy DNS dla domyślnej usługi w ApplicationManifest.xml
Otwórz projekt w programie Visual Studio lub w ulubionym edytorze, a następnie otwórz plik ApplicationManifest.xml. Przejdź do sekcji domyślnej usługi i dla każdej usługi, dodać `ServiceDnsName` atrybutu. Poniższy przykład pokazuje, jak można ustawić nazwy DNS usługi `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Gdy aplikacja zostanie wdrożona, wystąpienie usługi, w Eksploratorze usługi Service Fabric zawiera nazwę DNS dla tego wystąpienia, jak pokazano na poniższej ilustracji: 

![punkty końcowe usługi](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

W poniższym przykładzie ustawiono nazwy DNS dla usługi stanowej w celu `statefulsvc.app`. Usługa korzysta z nazwanych schematu partycjonowania. Należy zauważyć, że nazwy partycji są małe. Jest to wymagane dla partycji, które będą objęte zapytań DNS; Aby uzyskać więcej informacji, zobacz [zapytań DNS, dzięki czemu na stanowe usługi partycji](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ustawianie nazwy DNS dla usługi przy użyciu programu Powershell
Można ustawić nazwy DNS dla usługi, tworząc go za pomocą `New-ServiceFabricService` polecenia programu Powershell. Poniższy przykład tworzy nową usługę bezstanową z nazwą DNS `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Wersja zapoznawcza] Tworzenie zapytań DNS na partycji usługi stanowej
Począwszy od usługi Service Fabric w wersji 6.3, Usługa Service Fabric DNS obsługuje kwerendy dla partycji usługi.

Dla partycji, które będą używane w kwerendach DNS obowiązują następujące ograniczenia nazewnictwa:

   - Nazwy partycji powinien być zgodny z DNS.
   - Nazwy etykiet wielu partycji (zawierające kropka, ".", nazwa) nie powinna być używana.
   - Nazwy partycji powinien być małe.

Zapytania DNS, których platformą docelową partycji jest sformatowany w następujący sposób:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Gdzie:

- *First-Label-Of-Partitioned-Service-DNSName* jest pierwszą częścią nazwę DNS usługi.
- *PartitionPrefix* jest wartością, którą można określić w sekcji usługa DNS manifestu klastra, lub za pomocą szablonu usługi Resource Manager klastra. Wartość domyślna to "-". Aby dowiedzieć się więcej, zobacz [ustawień usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Nazwa w przypadku partycji docelowej* jest nazwa partycji. 
- *PartitionSuffix* jest wartością, którą można określić w sekcji usługa DNS manifestu klastra, lub za pomocą szablonu usługi Resource Manager klastra. Wartość domyślna to ciąg pusty. Aby dowiedzieć się więcej, zobacz [ustawień usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Pozostałe-podzielona na partycje-Service-DNSName* jest pozostała część nazwę DNS usługi.

W poniższych przykładach pokazano zapytań DNS dotyczących podzielonym na partycje usługi działające w ramach klastra, który zawiera ustawienia domyślne dla `PartitionPrefix` i `PartitionSuffix`: 

- Aby rozwiązać partycji "0" Usługa o nazwie DNS `backendrangedschemesvc.application` używającą ranged schematu partycjonowania, należy użyć `backendrangedschemesvc-0.application`.
- Aby rozwiązać partycji usługi o nazwie DNS "first" `backendnamedschemesvc.application` używającą o nazwie schematu partycjonowania, należy użyć `backendnamedschemesvc-first.application`.

Usługa DNS zwraca adres IP podstawowej repliki partycji. Jeśli żadna partycja jest określona, usługa zwraca adres IP w replice podstawowej losowo wybranej partycji.

## <a name="using-dns-in-your-services"></a>Przy użyciu systemu DNS w usługach
Jeśli wdrożono więcej niż jedna usługa, można znaleźć punktów końcowych inne usługi, aby komunikować się przy użyciu nazwy DNS. Usługa DNS działa w przypadku usług bezstanowych, a w usłudze Service Fabric wersji 6.3 i nowszym w przypadku usług stanowych. W przypadku usług stanowych działających w wersjach usługi Service Fabric przed 6.3 można użyć wbudowanego [odwrotnego serwera proxy usługi](./service-fabric-reverseproxy.md) dla połączeń http wywołać partycji określonej usługi. 

Porty dynamiczne nie są obsługiwane przez usługę DNS. Usługa zwrotnego serwera proxy do rozpoznania usług, które używają portów dynamicznych.

Poniższy kod przedstawia sposób wywołania usługi bezstanowej za pośrednictwem systemu DNS. Jest po prostu wywołania http regularne określane nazwę DNS, port i wszystkie ścieżce opcjonalne jako część adresu URL.

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

Poniższy kod pokazuje wywołanie na określonej partycji usługi stanowej. W takim przypadku nazwa DNS zawiera nazwę partycji (Partycja1). Wywołanie zakłada klastra z wartościami domyślnymi dla `PartitionPrefix` i `PartitionSuffix`.

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

## <a name="known-issues"></a>Znane problemy
* Dla usługi Service Fabric w wersji 6.3 i nowszej istnieje problem z wyszukiwania DNS dla nazwy usługi zawierające łącznik na nazwę DNS. Aby uzyskać więcej informacji na temat tego problemu, śledzenie następujących [problem w usłudze GitHub](https://github.com/Azure/service-fabric-issues/issues/1197). Rozwiązanie tego problemu będzie dostępna w ramach aktualizacji obok 6.3. 

* Usługi DNS dla usługi Service Fabric nie jest jeszcze obsługiwana w systemie Linux. Usługa DNS jest obsługiwana dla kontenerów w systemie Linux. Rozwiązanie ręczne przy użyciu sieci szkieletowej klienta/ServicePartitionResolver jest dostępna alternatywna.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat usługi komunikacji w ramach klastra za pomocą [łączenie i komunikować się z usługami](service-fabric-connect-and-communicate-with-services.md)

