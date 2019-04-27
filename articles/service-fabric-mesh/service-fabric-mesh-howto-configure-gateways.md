---
title: Konfigurowanie bramy kierujące żądania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować bramę, który obsługuje ruchem przychodzącym, aby Twoje aplikacje uruchomione na usługi Service Fabric siatki.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2e2502e35b3720ddbfe5950b89e2388de378f2ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583645"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurowanie zasobu bramy w celu kierowania żądań

Zasobu bramy jest używana kierującą ruch przychodzący z siecią, która przechowuje aplikacji. Skonfigurować tak, aby określić zasady, przez które żądania są kierowane do określonych usług lub punktów końcowych na podstawie struktury żądania. Zobacz [wprowadzenie do sieci w programie Service Fabric siatki](service-fabric-mesh-networks-and-gateways.md) Aby uzyskać więcej informacji na temat sieci i bramy w sieci. 

Zasoby bramy musi być zadeklarowany jako część szablonu wdrożenia (JSON lub yaml) i są zależne od zasobu sieciowego. W tym dokumencie opisano różne właściwości, które można ustawić dla bramy i obejmuje przykładowej konfiguracji bramy.

## <a name="options-for-configuring-your-gateway-resource"></a>Opcje dotyczące konfigurowania zasobu bramy

Ponieważ zasobu brama stanowi Most między siecią aplikacji i sieci podstawowej infrastruktury ( `open` sieci). Należy tylko skonfigurować jedną (w wersji zapoznawczej siatki, jest ograniczona do jednej bramy aplikacji). Deklaracja dla zasobu, który składa się z dwie główne części: metadanych zasobów i właściwości. 

### <a name="gateway-resource-metadata"></a>Metadane zasobu bramy

Brama jest zadeklarowana za pomocą następujące metadane:
* `apiVersion` -musi być ustawiona na "2018-09-01-preview" (lub później, w przyszłości)
* `name` — nazwy ciągu dla tej bramy
* `type` -"Microsoft.ServiceFabricMesh/gateways"
* `location` — powinna być ustawiona na lokalizacji aplikacji / sieci; zwykle będzie odwołania do parametru lokalizacji w danym wdrożeniu
* `dependsOn` -sieci, dla którego ta brama będzie służyć jako punkt danych przychodzących dla

Oto jak wygląda w szablonie wdrożenia usługi Azure Resource Manager (JSON): 

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

Sekcja właściwości służy do definiowania sieci, między którymi znajduje się brama i reguł routingu żądań. 

#### <a name="source-and-destination-network"></a>Źródłowe i docelowe sieci 

Każda brama wymaga `sourceNetwork` i `destinationNetwork`. Sieć źródłowa jest zdefiniowana jako sieć, z którego Twoja aplikacja będzie otrzymywać żądań przychodzących. Właściwość name powinny być zawsze ustawiony na "Otwarte". Sieć docelowa to sieć, która przeznaczonych do żądania. Wartość to nazwa powinna być równa nazwę zasobu w sieci lokalnej aplikacji (powinien zawierać pełną dokumentację do zasobu). Poniżej znajdują się przykładowe konfiguracji programu jak to wygląda dla wdrożenia w sieci o nazwie "myNetwork".

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

Brama może mieć wiele routingu reguły określające sposób przychodzący ruch będzie obsługiwana. Reguła routingu definiuje relację między port nasłuchujący i docelowy punkt końcowy dla danej aplikacji. W przypadku reguł routingu TCP istnieje mapowanie 1:1 między Port: punkt końcowy. Dla reguł routingu dla protokołu HTTP można ustawić bardziej złożone zasady routingu, które zbadać ścieżka żądania i opcjonalnie nagłówki, aby zdecydować, jak żądania będą kierowane. 

Reguły routingu są określane na podstawie poszczególnych portów. Każdy port transferu danych przychodzących ma swój własny tablicę reguł w sekcji właściwości w pliku config bramy. 

#### <a name="tcp-routing-rules"></a>Reguły routingu TCP 

Reguły routingu TCP składa się z następującymi właściwościami: 
* `name` — Odwołanie do regułę, która może być dowolnym ciągiem wybranych przez użytkownika 
* `port` -port do nasłuchiwania żądań przychodzących 
* `destination` -Specyfikacja punktu końcowego, który zawiera `applicationName`, `serviceName`, i `endpointName`, dla których żądania muszą być kierowane do

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


#### <a name="http-routing-rules"></a>Reguły routingu protokołu HTTP 

Reguły routingu HTTP składa się z następującymi właściwościami: 
* `name` — Odwołanie do regułę, która może być dowolnym ciągiem wybranych przez użytkownika 
* `port` -port do nasłuchiwania żądań przychodzących 
* `hosts` -Tablica zasady, które dotyczą żądań przychodzących do różnych "hosts" na porcie określonym powyżej. Hosty to zbiór aplikacji i usług, które mogą działać w sieci i mogą obsługiwać żądania przychodzące, czyli aplikacji sieci web. Zasady hosta są interpretowane w kolejności, dlatego należy utworzyć następujące malejąco poziomy specyficzności
    * `name` — Nazwa DNS hosta, dla którego określono następujące reguły routingu. Za pomocą "*" w tym miejscu utworzyć reguły routingu dla wszystkich hostów.
    * `routes` -Tablica zasad dla tego konkretnego hosta
        * `match` -na podstawie specyfikacji struktury żądania przychodzące dla tej reguły zastosować, `path`
            * `path` -zawiera `value` (przychodzący identyfikator URI), `rewrite` (jak chcesz, aby żądania, które mają być przekazywane) i `type` (obecnie może składać się wyłącznie "Prefiks")
            * `header` -jest tablicą opcjonalne nagłówki wartości do dopasowania w nagłówku żądania, że jeśli żądanie pasuje do specyfikacji ścieżki (powyżej).
              * Każdy wpis zawiera `name` (ciąg Nazwa nagłówka do dopasowania) `value` (wartość nagłówka w żądaniu ciągu) i `type` (obecnie może składać się wyłącznie "Exact")
        * `destination` -Jeśli żądanie jest zgodne, będą kierowane do tego miejsca docelowego, który jest określony przy użyciu `applicationName`, `serviceName`, i `endpointName`

Oto przykład HTTP reguły routingu będzie stosowane do żądania przychodzące na porcie 80, dla wszystkich hostów, obsługiwane przez aplikacje w tej sieci. Jeśli adres URL żądania zawiera strukturę, która pasuje do specyfikacji ścieżki, czyli `<IPAddress>:80/pickme/<requestContent>`, a następnie nastąpi przekierowanie do `myListener` punktu końcowego.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Przykład konfiguracji dla zasobu bramy 

Oto jak wygląda pełną konfigurację zasobu bramy (jest to zaczerpnięte z przykładowych danych przychodzących dostępne w [siatki przykłady repozytorium](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

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

Ta brama jest skonfigurowana dla aplikacji systemu Linux, "meshAppLinux", który składa się z co najmniej dwie usługi: "helloWorldService" i "counterService", która nasłuchuje na porcie 80. W zależności od struktury adresu URL żądania przychodzącego jej będzie kierować żądania do jednego z tych usług. 
* "\<Adres IP >: 80/helloWorld/\<żądania\>" spowodowałoby żądanie jest kierowane do "helloWorldListener" w helloWorldService. 
* "\<Adres IP >: 80/licznik/\<żądania\>" spowodowałoby żądanie jest kierowane do "counterListener" w counterService. 

## <a name="next-steps"></a>Kolejne kroki
* Wdrażanie [przykładowych danych przychodzących](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) można wyświetlić bramy w działaniu
