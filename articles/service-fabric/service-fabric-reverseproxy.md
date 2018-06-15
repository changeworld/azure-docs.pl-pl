---
title: Sieć szkieletowa usług Azure odwrotny serwer proxy | Dokumentacja firmy Microsoft
description: Użyj usługi sieć szkieletowa zwrotnego serwera proxy do komunikacji z mikrousług i spoza klastra.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 21e1e3041d7b1f4dc205355f6c0b8d4fd2e82775
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212279"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Zwrotny serwer proxy w sieci szkieletowej usług Azure
Zwrotny serwer proxy wbudowanych w sieci szkieletowej usług Azure pomaga mikrousług działającego w klastrze usługi sieć szkieletowa odnajdywania i komunikować się z innymi usługami, których punkty końcowe http.

## <a name="microservices-communication-model"></a>Model komunikacji Mikrousług
Mikrousług w sieci szkieletowej usług, uruchom na podzestaw węzłów w klastrze i można migrować między węzłami z różnych przyczyn. W związku z tym punktów końcowych dla mikrousług można zmienić dynamicznie. Aby wykryć i komunikować się z innymi usługami w klastrze, mikrousługi musi przechodzić przez następujące kroki:

1. Rozwiązania lokalizacji usługi za pomocą usługi nazw.
2. Połączyć się z usługą.
3. Zawijanie powyższych kroków w pętli, które implementuje rozpoznawania usługi, a następnie spróbuj ponownie zasad można stosować na awarie połączenia

Aby uzyskać więcej informacji, zobacz [Connect i łączyć się z usługami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikacji przy użyciu zwrotnego serwera proxy
Zwrotny serwer proxy to usługa, która działa na każdym węźle i obsługi rozwiązania punktu końcowego automatyczny i inne błędy połączeń w imieniu klienta usługi. Zwrotny serwer proxy można skonfigurować do stosowania różnych zasad, ponieważ obsługuje on żądania z klienta usług. Przy użyciu zwrotnego serwera proxy umożliwia usługę klienta, aby korzystać z żadnych bibliotek komunikacji HTTP po stronie klienta i nie wymagają specjalnych rozdzielczości i spróbuj logiki w usłudze. 

Zwrotny serwer proxy przedstawia jedną lub więcej punktów końcowych na lokalny węzeł usługi klienta używany do wysyłania żądań do innych usług.

![Wewnętrznej komunikacji][1]

> [!NOTE]
> **Obsługiwane platformy**
>
> Zwrotny serwer proxy w sieci szkieletowej usług obecnie obsługuje następujące platformy
> * *Klaster systemu Windows*: system Windows 8 lub nowszym lub Windows Server 2012 i nowsze
> * *Klaster systemu Linux*: wstecznego serwera Proxy nie jest obecnie dostępna w przypadku klastrów systemu Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Osiągnięcia mikrousług z spoza klastra
Domyślny model komunikacji zewnętrznej dla mikrousług to model opcjonalnych, gdzie każdego nie można uzyskać dostępu do usługi bezpośrednio z klientami zewnętrznymi. [Moduł równoważenia obciążenia Azure](../load-balancer/load-balancer-overview.md), czyli granicę sieci między mikrousług i klientami zewnętrznymi, wykonuje translatora adresów sieciowych i przekazuje zewnętrznych żądań do wewnętrznego IP:port punktów końcowych. Aby punkt końcowy mikrousługi bezpośrednio dostępny dla klientów zewnętrznych, najpierw należy skonfigurować usługi równoważenia obciążenia, aby przesyłał dalej ruch na każdy port używany przez usługę w klastrze. Ponadto większość mikrousług, szczególnie stanowe mikrousług, nie na żywo na wszystkich węzłach klastra. Mikrousług można przenosić między węzłami w tryb failover. W takich przypadkach usługi równoważenia obciążenia skutecznie nie można określić lokalizacji węzeł docelowy dla replik, do których należy przekazywać ruch.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Osiągnięcia mikrousług przez zwrotny serwer proxy z spoza klastra
Zamiast konfigurować port poszczególnych usług w usłudze równoważenia obciążenia, można skonfigurować tylko port zwrotnego serwera proxy w usłudze równoważenia obciążenia. Taka konfiguracja pozwala klientom poza klastrem reach usługi wewnątrz klastra przy użyciu zwrotnego serwera proxy bez dodatkowej konfiguracji.

![Komunikacji zewnętrznej][0]

> [!WARNING]
> Po skonfigurowaniu portu zwrotnego serwera proxy w usłudze równoważenia obciążenia wszystkie mikrousług w klastrze, które udostępniają punkt końcowy HTTP adresowane z spoza klastra.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Format identyfikatora URI do adresowania usług przy użyciu zwrotnego serwera proxy
Zwrotny serwer proxy używa formatu określonego uniform resource identifier (URI) do identyfikowania partycji usługi, do którego powinny zostać przekazane żądania przychodzącego, jeśli:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** zwrotny serwer proxy można skonfigurować do akceptowania ruchu HTTP lub HTTPS. Przekazywanie protokołu HTTPS, można znaleźć w temacie [nawiązywanie bezpiecznych usługi za pomocą zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md) po instalacji zwrotnego serwera proxy do nasłuchiwania protokołu HTTPS.
* **Klaster pełną nazwę domeny (FQDN) | wewnętrznym adresem IP:** dla klientów zewnętrznych, można skonfigurować zwrotnego serwera proxy, aby był dostępny za pośrednictwem domeny klastra, na przykład mycluster.eastus.cloudapp.azure.com. Domyślnie zwrotny serwer proxy działa na każdym węźle. Dla wewnętrznej ruchu zwrotny serwer proxy można połączyć się z hosta lokalnego lub na dowolny węzeł wewnętrzny adres IP, np. 10.0.0.1.
* **Port:** jest to port, takich jak 19081, który został określony dla zwrotnego serwera proxy.
* **ServiceInstanceName:** jest to pełna nazwa wystąpienia wdrożonej usługi, które próbujesz nawiązać połączenie bez "fabric: /" schematu. Na przykład, aby osiągnąć *fabric: / myapp/Moja_usługa/* usługi, należy użyć *myapp/Moja_usługa*.

    Nazwa wystąpienia usługi jest rozróżniana wielkość liter. Przy użyciu innej wielkości znaków dla nazwy wystąpienia usługi w adresie URL powoduje, że żądania z 404 (nie znaleziono).
* **Sufiks ścieżki:** to rzeczywiste ścieżki adresu URL, takie jak *myapi/wartości/Dodaj/3*, usługi, który chcesz nawiązać połączenie.
* **PartitionKey:** usługi podzielonym na partycje, jest to klucz partycji obliczanej partycji, którą chcesz nawiązać połączenie. Należy pamiętać, że jest to *nie* partycji Identyfikatora GUID. Ten parametr nie jest wymagana w przypadku usług korzystających z pojedynczą schemat partycji.
* **PartitionKind:** to schemat partycji usługi. Może to być "Int64Range" lub "O nazwie". Ten parametr nie jest wymagana w przypadku usług korzystających z pojedynczą schemat partycji.
* **ListenerName** punkty końcowe usługi są w formie {"Punkty końcowe": {"Listener1": "Punk końcowy 1", "Listener2": "Punk końcowy 2"...}}. Gdy usługa udostępnia wiele punktów końcowych, identyfikuje punktu końcowego, które powinny zostać przekazane żądania klienta. Ten można pominąć, jeśli usługa ma tylko jeden odbiornik.
* **TargetReplicaSelector** Określa, jak należy wybrać docelowej repliki lub wystąpienia.
  * Gdy Usługa docelowa jest stanowa, TargetReplicaSelector może być jedną z następujących: "PrimaryReplica", "RandomSecondaryReplica" lub "RandomReplica". Jeśli ten parametr nie jest określony, wartością domyślną jest "PrimaryReplica".
  * Gdy Usługa docelowa jest bezstanowych, zwrotny serwer proxy wybiera losowe wystąpienia partycji usługi do przesyłania żądania.
* **Limit czasu:** określa limit czasu dla żądania HTTP utworzone przez zwrotny serwer proxy z usługą w imieniu żądania klienta. Wartość domyślna to 60 sekund. Jest to parametr opcjonalny.

### <a name="example-usage"></a>Przykład użycia
Na przykład Przyjrzyjmy *fabric: / MyApp/Moja_usługa* usługi, która otwiera odbiornik HTTP na następujący adres URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Poniżej przedstawiono zasoby usługi:

* `/index.html`
* `/api/users/<userId>`

Jeśli usługa używa singleton schemat partycjonowania *PartitionKey* i *PartitionKind* parametrów ciągu zapytania nie są wymagane, a usługa jest osiągalna przy użyciu bramy jako:

* Zewnętrznie: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Wewnętrznie: `http://localhost:19081/MyApp/MyService`

Jeśli usługa używa Int64 jednolity schemat partycjonowania *PartitionKey* i *PartitionKind* parametrów ciągu zapytania musi być używany do osiągnięcia partycji usługi:

* Zewnętrznie: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Aby uzyskać dostęp do zasobów, które udostępnia usługi, po prostu umieść ścieżka zasobu po nazwie usługi w adresie URL:

* Zewnętrznie: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Bramy następnie będzie przekazywał te żądania na adres URL usługi:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Specjalnej obsługi podczas udostępniania portów usług
Zwrotny serwer proxy usługi sieci szkieletowej próbuje rozpoznać adres usługi ponownie i ponów żądanie, gdy nie można osiągnąć usługi. Ogólnie rzecz biorąc gdy nie można połączyć się z usługą, wystąpienie usługi lub replika została przeniesiona do innego węzła w ramach jego normalnej cyklu życia. W takim przypadku zwrotny serwer proxy może zostać wyświetlony błąd połączenia sieciowego wskazujący, że punkt końcowy nie jest już otwarty na pierwotnie rozpoznany adres.

Jednak repliki lub wystąpień usługi można udostępniać procesu hosta i może również udostępniać port obsługiwanych przez serwer sieci web opartych na pliku http.sys w tym:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener platformy ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

W takiej sytuacji jest serwer sieci web jest dostępna w proces hosta i odpowiada na żądania, że wystąpienie usługi rozwiązane lub replika nie jest już dostępny na hoście. W takim przypadku brama zostanie wyświetlony komunikat odpowiedzi HTTP 404 z serwera sieci web. W związku z tym odpowiedzi HTTP 404 może mieć dwie różne znaczenie:

- W przypadku #1: Adres usługi jest poprawny, ale zasób, który użytkownik zażądał nie istnieje.
- Przypadek #2: Adres usługi jest nieprawidłowa, i zasobów, które użytkownik zażądał może istnieć na inny węzeł.

Pierwszym przypadku jest normalne 404 protokołu HTTP, która jest uznawana za błąd użytkownika. Jednak w drugim przypadku użytkownik zażądał z zasobem, który istnieje. Zwrotny serwer proxy nie może zlokalizować go, ponieważ sama usługa została przeniesiona. Zwrotny serwer proxy musi rozpoznać adresu ponownie i ponów żądanie.

Zwrotny serwer proxy w związku z tym musi mieć możliwość rozróżnienia tych przypadków. Aby tej różnicy, wskazówkę z serwera jest wymagany.

* Domyślnie zwrotny serwer proxy zakłada przypadku #2 i próbuje rozpoznać i ponownie wystawić żądania.
* Wskaż, wielkości liter #1, aby zwrotnego serwera proxy, usługa powinny zostać zwrócone następujące nagłówka odpowiedzi HTTP:

  `X-ServiceFabric : ResourceNotFound`

Ten nagłówek odpowiedzi HTTP wskazuje normalnej sytuacji HTTP 404, w której żądany zasób nie istnieje, a zwrotnego serwera proxy nie będzie próbował rozpoznać adresu usługi ponownie.

## <a name="setup-and-configuration"></a>Instalacja i Konfiguracja

### <a name="enable-reverse-proxy-via-azure-portal"></a>Włącz zwrotnego serwera proxy za pośrednictwem portalu Azure

Azure portal udostępnia opcję, aby włączyć zwrotnego serwera proxy podczas tworzenia nowego klastra sieci szkieletowej usług.
W obszarze **klastra tworzenia sieci szkieletowej usług**, krok 2: konfigurację klastra, konfiguracja typu węzła, zaznacz pola wyboru "Włącz zwrotnego serwera proxy".
Do konfigurowania bezpiecznej zwrotnego serwera proxy, można określić certyfikat SSL w kroku 3: zabezpieczeń, konfigurowanie ustawień zabezpieczeń klastra, zaznacz pole wyboru "Obejmują certyfikat protokołu SSL dla zwrotnego serwera proxy" i wprowadź szczegóły certyfikatu.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Włącz zwrotny serwer proxy przy użyciu szablonów usługi Azure Resource Manager

Można użyć [szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) umożliwiające zwrotnego serwera proxy w sieci szkieletowej usług dla klastra.

Zapoznaj się [skonfigurować HTTPS zwrotnego serwera Proxy w klastrze bezpiecznego](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) dla usługi Azure Resource Manager przykłady szablonu można skonfigurować bezpiecznego odwrotny serwer proxy z certyfikatu i obsługa Przerzucanie certyfikatów.

Najpierw Pobierz szablon dla klastra, który chcesz wdrożyć. Można korzystać z przykładowych szablonów lub utworzyć niestandardowy szablon usługi Resource Manager. Następnie można włączyć zwrotny serwer proxy przy użyciu następujących kroków:

1. Zdefiniuj port dla zwrotnego serwera proxy w [sekcji parametrów](../azure-resource-manager/resource-group-authoring-templates.md) szablonu.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Określ numer portu dla każdego z obiektów nodetype **klastra** [sekcji Typ zasobu](../azure-resource-manager/resource-group-authoring-templates.md).

    Port jest identyfikowane przez nazwę parametru reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Aby adres zwrotny serwer proxy z poza klaster platformy Azure, należy skonfigurować reguły modułu równoważenia obciążenia Azure portu, który określono w kroku 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Aby skonfigurować certyfikaty SSL na porcie dla zwrotnego serwera proxy, należy dodać certyfikatu do ***reverseProxyCertificate*** właściwości w **klastra** [sekcji Typ zasobu](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Obsługa certyfikatu zwrotny serwer proxy, który różni się od certyfikatu klastra
 Jeśli certyfikat zwrotnego serwera proxy jest inna niż certyfikatu, która zabezpiecza klastra, następnie wcześniej określony certyfikat należy można zainstalowany na maszynie wirtualnej i dodane do listy kontroli dostępu (ACL), tak aby usługi sieci szkieletowej do niego dostęp. Można to zrobić **virtualMachineScaleSets** [sekcji Typ zasobu](../resource-group-authoring-templates.md). Do instalacji należy dodać ten certyfikat do osProfile. Sekcja rozszerzenia szablonu można aktualizować certyfikatu na liście ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Korzystając z certyfikatów, które różnią się od certyfikatu klastra umożliwia zwrotny serwer proxy istniejącego klastra, należy zainstalować certyfikat zwrotnego serwera proxy i zaktualizować listy ACL w klastrze, przed włączeniem zwrotnego serwera proxy. Zakończenie [szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) wdrożenia przy użyciu ustawień wymienione wcześniej przed rozpoczęciem wdrażania, aby włączyć zwrotnego serwera proxy w kroki 1 – 4.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz przykład protokołu HTTP do komunikacji między usługami w [przykładowy projekt w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Przekazywanie danych do bezpiecznej usługi HTTP przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Zdalne wywołania procedur z usług zdalnych niezawodne usługi](service-fabric-reliable-services-communication-remoting.md)
* [Interfejs API, który używa OWIN w niezawodnej usługi sieci Web](service-fabric-reliable-services-communication-webapi.md)
* [Komunikacyjny WCF za pomocą niezawodnych usług](service-fabric-reliable-services-communication-wcf.md)
* Opcje konfiguracji dodatkowych zwrotnego serwera proxy, można znaleźć w części bramy aplikacji/Http [ustawienia klastra dostosować sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
