---
title: Usługa DNS sieci szkieletowej usług Azure
description: Użyj usługi dns sieci szkieletowej usług do odnajdowania mikrousług z wewnątrz klastra.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458035"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS Service in Azure Service Fabric (Usługa DNS w usłudze Azure Service Fabric)
Usługa DNS to opcjonalna usługa systemowa, którą można włączyć w klastrze w celu odnajdowania innych usług przy użyciu protokołu DNS. 

Wiele usług, zwłaszcza konteneryzowanych, można adresować za pośrednictwem istniejącego adresu URL. Jest w stanie rozpoznać te usługi przy użyciu standardowego protokołu DNS, a nie protokół usługi nazewnictwa sieci szkieletowej usług, jest pożądane. Usługa DNS umożliwia mapowanie nazw DNS na nazwę usługi, a tym samym rozpoznawanie adresów IP punktu końcowego. Taka funkcja zachowuje przenośność konteneryzowanych usług na różnych platformach i może ułatwić scenariusze "lift and shift", umożliwiając korzystanie z istniejących adresów URL usług, zamiast przepisywać kod w celu wykorzystania usługi nazewnictwa. 

Usługa DNS mapuje nazwy DNS na nazwy usług, które z kolei są rozpoznawane przez usługę nazewnictwa w celu zwrócenia punktu końcowego usługi. Nazwa DNS usługi jest podana w momencie tworzenia. Na poniższym diagramie pokazano, jak działa usługa DNS dla usług bezstanowych.

![punkty końcowe usługi](./media/service-fabric-dnsservice/stateless-dns.png)

Począwszy od sieci szkieletowej usług w wersji 6.3, protokół DNS sieci szkieletowej usług został rozszerzony o schemat adresowania podzielonych na partycje usług stanowych. Rozszerzenia te umożliwiają rozpoznawanie określonych adresów IP partycji przy użyciu kombinacji nazwy DNS usługi stanowej i nazwy partycji. Obsługiwane są wszystkie trzy schematy partycjonowania:

- Partycjonowanie nazwane
- Partycjonowanie z dystansem
- Partycjonowanie singleton

Na poniższym diagramie pokazano, jak działa usługa DNS dla podzielonych na partycje usług stanowych.

![punkty końcowe usługi stanowej](./media/service-fabric-dnsservice/stateful-dns.png)

Porty dynamiczne nie są obsługiwane przez usługę DNS. Aby rozwiązać usługi udostępniane na portach dynamicznych, należy użyć [usługi odwrotnego serwera proxy](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Włączanie usługi DNS
> [!NOTE]
> Usługa DNS dla usług sieci szkieletowej usług nie jest jeszcze obsługiwana w systemie Linux.

Podczas tworzenia klastra przy użyciu portalu usługa DNS jest domyślnie włączona w polu wyboru **Dołącz usługę DNS** w menu Konfiguracji **klastra:**

![Włączanie usługi DNS za pośrednictwem portalu](./media/service-fabric-dnsservice/enable-dns-service.png)

Jeśli nie używasz portalu do tworzenia klastra lub aktualizujesz istniejący klaster, musisz włączyć usługę DNS w szablonie:

- Aby wdrożyć nowy klaster, można użyć [przykładowych szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) lub utworzyć własny szablon Menedżera zasobów. 
- Aby zaktualizować istniejący klaster, można przejść do grupy zasobów klastra w portalu i kliknąć przycisk **Skrypt automatyzacji,** aby pracować z szablonem odzwierciedlanym stanem klastra i innymi zasobami w grupie. Aby dowiedzieć się więcej, zobacz [Eksportowanie szablonu z grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Po nadaniu szablonu można włączyć usługę DNS, wykonując następujące czynności:

1. Sprawdź, `apiversion` czy jest `2017-07-01-preview` ustawiona `Microsoft.ServiceFabric/clusters` lub późniejsza dla zasobu, a jeśli nie, zaktualizuj go w następującym przykładzie:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz włącz usługę DNS w jeden z następujących sposobów:

   - Aby włączyć usługę DNS z ustawieniami `addonFeatures` domyślnymi, `properties` dodaj ją do sekcji wewnątrz sekcji, jak pokazano w poniższym przykładzie:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Aby włączyć usługę z ustawieniami innymi `DnsService` niż `fabricSettings` domyślne, `properties` dodaj sekcję do sekcji wewnątrz sekcji. W takim przypadku nie trzeba dodawać usługi `addonFeatures`DnsService do . Aby dowiedzieć się więcej o właściwościach, które można ustawić dla usługi DNS, zobacz [Ustawienia usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. Po zaktualizowaniu szablonu klastra o wprowadzone zmiany zastosuj je i pozwól na zakończenie uaktualnienia. Po zakończeniu uaktualniania usługa systemu DNS zostanie uruchomiona w klastrze. Nazwa usługi `fabric:/System/DnsService`jest , i można ją znaleźć w sekcji Usługi **systemu** w Eksploratorze sieci szkieletowej usług. 

> [!NOTE]
> Podczas uaktualniania systemu DNS z wyłączonego do włączonego Eksplorator sieci szkieletowej usług może nie odzwierciedlać nowego stanu. Aby rozwiązać, uruchom ponownie węzły, modyfikując zasadę uaktualnienia w szablonie usługi Azure Resource Manager. Zobacz [odwołanie szablonu sieci szkieletowej usług, aby](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) uzyskać więcej informacji.

> [!NOTE]
> Włączenie usługi DNS podczas tworzenia na komputerze lokalnym spowoduje zastąpienie niektórych ustawień DNS. Jeśli występują problemy z połączeniem z Internetem, sprawdź ustawienia DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Ustawianie nazwy DNS usługi
Można ustawić nazwę DNS dla usług deklaratywnie dla usług domyślnych w pliku ApplicationManifest.xml lub za pomocą poleceń programu PowerShell.

Nazwa DNS usługi można rozwiązać w całym klastrze, dlatego ważne jest, aby zapewnić unikatowość nazwy DNS w klastrze. 

Zdecydowanie zaleca się stosowanie schematu `<ServiceDnsName>.<AppInstanceName>`nazewnictwa ; na przykład `service1.application1`. Jeśli aplikacja jest wdrażana przy użyciu kompozycji platformy Docker, usługi są automatycznie przypisywane nazwy DNS przy użyciu tego schematu nazewnictwa.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ustawianie nazwy DNS dla usługi domyślnej w pliku ApplicationManifest.xml
Otwórz projekt w programie Visual Studio lub ulubionym edytorze i otwórz plik ApplicationManifest.xml. Przejdź do sekcji usług domyślnych i `ServiceDnsName` dla każdej usługi dodaj atrybut. W poniższym przykładzie pokazano, jak ustawić nazwę DNS usługi na`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Po wdrożeniu aplikacji wystąpienie usługi w Eksploratorze sieci szkieletowej usług pokazuje nazwę DNS dla tego wystąpienia, jak pokazano na poniższym rysunku: 

![punkty końcowe usługi](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

W poniższym przykładzie ustawia nazwę DNS `statefulsvc.app`usługi stanowej na . Usługa używa nazwanego schematu partycjonowania. Należy zauważyć, że nazwy partycji są małe litery. Jest to wymagane dla partycji, które będą kierowane w kwerendach DNS; Aby uzyskać więcej informacji, zobacz [Wykonywanie zapytań DNS na partycji usługi stanowej](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ustawianie nazwy DNS usługi przy użyciu programu Powershell
Nazwę DNS usługi można ustawić podczas tworzenia jej `New-ServiceFabricService` za pomocą polecenia programu Powershell. Poniższy przykład tworzy nową usługę bezstanową o nazwie DNS`service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Podgląd] Wykonywanie zapytań DNS na partycji usługi stanowej
Począwszy od sieci szkieletowej usług w wersji 6.3, usługa DNS sieci szkieletowej usług obsługuje zapytania dotyczące partycji usług.

W przypadku partycji, które będą używane w kwerendach DNS, obowiązują następujące ograniczenia nazewnictwa:

   - Nazwy partycji powinny być zgodne z systemem DNS.
   - Nazwy partycji wielu etykiet (które zawierają kropkę, '.', w nazwie) nie powinny być używane.
   - Nazwy partycji powinny być małe litery.

Kwerendy DNS przeznaczone dla partycji są sformatowane w następujący sposób:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Gdzie:

- *First-Label-Of-Partitioned-Service-DNSName* jest pierwszą częścią nazwy DNS usługi.
- *PartitionPrefix* to wartość, którą można ustawić w sekcji DnsService manifestu klastra lub za pośrednictwem szablonu Menedżera zasobów klastra. Wartością domyślną jest "--". Aby dowiedzieć się więcej, zobacz [Ustawienia usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Nazwa docelowa-partycja* to nazwa partycji. 
- *PartitionSuffix* jest wartością, którą można ustawić w sekcji DnsService manifestu klastra lub za pośrednictwem szablonu Menedżera zasobów klastra. Wartością domyślną jest pusty ciąg. Aby dowiedzieć się więcej, zobacz [Ustawienia usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Pozostała usługa partycjonowana-DNSName* jest pozostałą częścią nazwy DNS usługi.

W poniższych przykładach przedstawiono kwerendy DNS dotyczące usług podzielonych na partycje działających w klastrze, który ma ustawienia domyślne dla `PartitionPrefix` i: `PartitionSuffix` 

- Aby rozwiązać partycję "0" usługi `backendrangedschemesvc.application` o nazwie DNS, która używa `backendrangedschemesvc-0.application`schematu partycjonowania dystansowego, należy użyć programu .
- Aby rozwiązać partycję "pierwszy" usługi `backendnamedschemesvc.application` o nazwie DNS, która `backendnamedschemesvc-first.application`używa nazwanego schematu partycjonowania, należy użyć programu .

Usługa DNS zwraca adres IP repliki podstawowej partycji. Jeśli nie określono partycji, usługa zwraca adres IP repliki podstawowej losowo wybranej partycji.

## <a name="using-dns-in-your-services"></a>Korzystanie z systemu DNS w usługach
Jeśli wdrożysz więcej niż jedną usługę, można znaleźć punkty końcowe innych usług do komunikowania się przy użyciu nazwy DNS. Usługa DNS działa dla usług bezstanowych, a w sieci szkieletowej usług w wersji 6.3 i nowszej dla usług stanowych. W przypadku usług stanowych uruchomionych w wersjach sieci szkieletowej usług przed wersją 6.3 można użyć wbudowanej [usługi odwrotnego serwera proxy](./service-fabric-reverseproxy.md) dla wywołań http do wywołania określonej partycji usługi. 

Porty dynamiczne nie są obsługiwane przez usługę DNS. Za pomocą usługi odwrotnego serwera proxy można rozpoznawać usługi korzystające z portów dynamicznych.

Poniższy kod pokazuje, jak wywołać usługę bezstanową za pośrednictwem systemu DNS. Jest to po prostu regularne wywołanie http, w którym podasz nazwę DNS, port i dowolną opcjonalną ścieżkę jako część adresu URL.

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

Poniższy kod pokazuje wywołanie określonej partycji usługi stanowej. W takim przypadku nazwa DNS zawiera nazwę partycji (partition1). Wywołanie zakłada klaster z `PartitionPrefix` wartościami domyślnymi dla i `PartitionSuffix`.

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
* W przypadku sieci szkieletowej usług w wersji 6.3 lub nowszej występuje problem z wyszukiwaniem DNS nazw usług zawierających łącznik w nazwie DNS. Aby uzyskać więcej informacji na ten temat, prześlij następujący [problem z githubem.](https://github.com/Azure/service-fabric-issues/issues/1197) Poprawka do tego jest już w następnej aktualizacji 6.3. 

* Usługa DNS dla usług sieci szkieletowej usług nie jest jeszcze obsługiwana w systemie Linux. Usługa DNS jest obsługiwana dla kontenerów w systemie Linux. Ręczne rozpoznawanie przy użyciu klienta sieci szkieletowej/ServicePartitionResolver jest dostępna alternatywa.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o komunikacji serwisowej w klastrze dzięki [łączeniu się i komunikacji z usługami](service-fabric-connect-and-communicate-with-services.md)

