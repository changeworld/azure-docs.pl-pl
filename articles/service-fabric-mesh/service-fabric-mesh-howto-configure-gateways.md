---
title: Konfigurowanie bramy do kierowania żądań
description: Dowiedz się, jak skonfigurować bramę obsługującą ruch przychodzący dla aplikacji działających w sieci szkieletowej usług.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461953"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurowanie zasobu bramy do rozsyłania żądań

Zasób bramy jest używany do kierowania ruchu przychodzącego do sieci, w której znajduje się aplikacja. Skonfiguruj go, aby określić reguły, za pomocą których żądania są kierowane do określonych usług lub punktów końcowych na podstawie struktury żądania. Zobacz [Wprowadzenie do sieci w sieci szkieletowej usług Mesh, aby](service-fabric-mesh-networks-and-gateways.md) uzyskać więcej informacji na temat sieci i bram w aplikacji Mesh. 

Zasoby bramy muszą być zadeklarowane jako część szablonu wdrożenia (JSON lub yaml) i są zależne od zasobu sieciowego. W tym dokumencie opisano różne właściwości, które można ustawić dla bramy i obejmuje przykładową konfigurację bramy.

## <a name="options-for-configuring-your-gateway-resource"></a>Opcje konfigurowania zasobu bramy

Ponieważ zasób bramy służy jako pomost między siecią aplikacji a `open` siecią podstawowej infrastruktury (siecią). Należy tylko skonfigurować jeden (w podglądzie siatki, istnieje limit jednej bramy na aplikację). Deklaracja dla zasobu składa się z dwóch głównych części: metadanych zasobów i właściwości. 

### <a name="gateway-resource-metadata"></a>Metadane zasobów bramy

Brama jest zadeklarowana z następującymi metadanymi:
* `apiVersion`- musi być ustawiona na "2018-09-01-preview" (lub później, w przyszłości)
* `name`- nazwa ciągu dla tej bramy
* `type`- "Microsoft.ServiceFabricMesh/gateways"
* `location`- powinny być ustawione na lokalizację aplikacji / sieci; zwykle będzie to odniesienie do parametru lokalizacji we wdrożeniu
* `dependsOn`- sieć, dla której brama ta będzie służyć jako punkt

Oto jak wygląda w szablonie wdrażania usługi Azure Resource Manager (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Właściwości bramy

Sekcja właściwości służy do definiowania sieci, między którymi leży brama, oraz reguł dla żądań routingu. 

#### <a name="source-and-destination-network"></a>Sieć źródło-miejsce docelowe 

Każda brama `sourceNetwork` `destinationNetwork`wymaga a i . Sieć źródłowa jest definiowana jako sieć, z której aplikacja będzie odbierać żądania przychodzące. Jego właściwość name powinna być zawsze ustawiona na "Otwórz". Sieć docelowa to sieć kierowana na żądania. Wartość nazwy dla tego należy ustawić na nazwę zasobu sieci lokalnej aplikacji (powinny zawierać pełne odwołanie do zasobu). Zobacz poniżej przykładową konfiżę tego, jak to wygląda w przypadku wdrożenia w sieci o nazwie "myNetwork".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Reguły 

Brama może mieć wiele reguł routingu, które określają sposób obsługi ruchu przychodzącego. Reguła routingu definiuje relację między portem nasłuchiwania a docelowym punktem końcowym dla danej aplikacji. W przypadku reguł routingu TCP istnieje mapowanie 1:1 między Port:Endpoint. W przypadku reguł routingu HTTP można ustawić bardziej złożone reguły routingu, które badają ścieżkę żądania i opcjonalnie nagłówki, aby zdecydować, jak żądanie zostanie rozesłane. 

Reguły routingu są określone na podstawie portu. Każdy port przychodzący ma własną tablicę reguł w sekcji właściwości konfiguracji bramy. 

#### <a name="tcp-routing-rules"></a>Reguły routingu TCP 

Reguła routingu TCP składa się z następujących właściwości: 
* `name`- odniesienie do reguły, która może być dowolny ciąg wyboru 
* `port`- port do nasłuchiwać przychodzących żądań 
* `destination`- specyfikacja punktu `applicationName` `serviceName`końcowego, która obejmuje, oraz `endpointName`, w przypadku gdy wnioski muszą być kierowane do

Oto przykładowa reguła routingu TCP:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Reguły routingu HTTP 

Reguła routingu HTTP składa się z następujących właściwości: 
* `name`- odniesienie do reguły, która może być dowolny ciąg wyboru 
* `port`- port do nasłuchiwać przychodzących żądań 
* `hosts`- tablica zasad, które mają zastosowanie do żądań przychodzących do różnych "hostów" na porcie określonym powyżej. Hosty to zestaw aplikacji i usług, które mogą być uruchomione w sieci i mogą obsługiwać przychodzące żądania, czyli aplikację internetową. Zasady hosta są interpretowane w kolejności, więc należy utworzyć następujące w malejąco poziomów specyficzności
    * `name`- nazwę DNS hosta, dla którego określono następujące reguły routingu. Użycie "*" w tym miejscu spowoduje utworzenie reguł routingu dla wszystkich hostów.
    * `routes`- szereg zasad dla tego konkretnego hosta
        * `match`- określenie struktury przychodzącego żądania stosowania tej zasady, w oparciu o`path`
            * `path`- zawiera `value` (przychodzące `rewrite` identyfikatory URI), (jak chcesz, `type` aby żądanie zostało przekazane dalej) i (obecnie może to być tylko "Prefiks")
            * `header`- jest opcjonalną tablicą wartości nagłówków, które są zgodne w nagłówku żądania, które jeśli żądanie jest zgodne ze specyfikacją ścieżki (powyżej).
              * każdy wpis `name` zawiera (nazwa ciągu nagłówka do dopasowania), `value` (wartość ciągu `type` nagłówka w żądaniu) i a (obecnie może to być tylko "Exact")
        * `destination`- jeśli żądanie będzie zgodne, zostanie ono skierowane do `applicationName`tego `serviceName`miejsca docelowego, które jest określone za pomocą , , i`endpointName`

Oto przykładowa reguła routingu HTTP, która miałaby zastosowanie do żądań przychodzących na porcie 80, do wszystkich hostów obsługiwanych przez aplikacje w tej sieci. Jeśli adres URL żądania ma strukturę, która pasuje `<IPAddress>:80/pickme/<requestContent>`do specyfikacji ścieżki, `myListener` to znaczy , a następnie zostanie skierowany do punktu końcowego.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Przykładowa konfigura dla zasobu bramy 

Oto, jak wygląda pełna config zasobów bramy (jest to zaadaptowane z próbki transferu danych przychodzących dostępnych w [repozytorium próbek siatki):](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Ta brama jest skonfigurowana dla aplikacji systemu Linux ,,meshAppLinux", która składa się z co najmniej dwóch usług, "helloWorldService" i "counterService", która nasłuchuje na porcie 80. W zależności od struktury adresu URL żądania przychodzącego będzie ono kierowane do jednej z tych usług. 
* "\<IPAddress>:80/helloWorld/\<request\>" spowodowałoby żądanie jest kierowane do "helloWorldListener" w helloWorldService. 
* "\<IPAddress>:80/counter/\<request\>" spowodowałoby skierowanie żądania do "counterListener" w counterService. 

## <a name="next-steps"></a>Następne kroki
* Wdrażanie [przykładu transferu przychodzącego,](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) aby wyświetlić bramy w akcji
