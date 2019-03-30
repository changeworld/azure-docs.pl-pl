---
title: Konfigurowanie tryby sieci dla usługi kontenerów Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować różne tryby sieci, które są obsługiwane przez usługę Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 6f14b3184cabd1dfd84f04260f6b8c831037cbcf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668130"
---
# <a name="service-fabric-container-networking-modes"></a>Tryby sieci kontenera usługi Service Fabric

Klaster usługi Azure Service Fabric dla kontenera usługi używa **translatora adresów sieciowych** tryb sieci domyślnie. Gdy więcej niż jedna usługa kontener nasłuchuje na ten sam port jest używany tryb translatora adresów sieciowych, mogą wystąpić błędy wdrażania. Aby zapewnić obsługę wielu usług kontener nasłuchuje na tym samym porcie, Usługa Service Fabric oferuje **Otwórz** tryb sieci (w wersji 5.7 lub nowszej). W trybie otwartego każda usługa kontenera ma wewnętrzny dynamicznie przypisany adres IP, który obsługuje wiele usług, które nasłuchuje na tym samym porcie.  

Jeśli masz jedną usługę kontenera przy użyciu statycznego punktu końcowego w manifeście usługi można utworzysz i usuniesz nowe usługi przy użyciu Otwórz tryb bez błędy związane z wdrażaniem. Tego samego pliku docker-compose.yml można również za pomocą mapowania portu statycznego do utworzenia wielu usług.

Gdy z nich usługę kontenera powoduje ponowne uruchomienie lub przechodzi do innego węzła w klastrze, adres IP zmienia się. Z tego powodu nie zaleca się przy użyciu przydzielony dynamicznie adres IP usługi kontenerów. Dla potrzeb odnajdowania usługi należy używać tylko Usługa nazewnictwa usługi Service Fabric lub usługi DNS. 

>[!WARNING]
>Platforma Azure zezwoli daje w sumie 65 356 adresy IP na sieć wirtualną. Suma liczba węzłów i liczbę wystąpień usługi kontenera, (które korzystają z Otwórz tryb) nie może przekraczać 65 356 adresów IP w sieci wirtualnej. W przypadku scenariuszy o wysokiej gęstości, firma Microsoft zaleca tryb sieci translatora adresów sieciowych. Ponadto inne zależności, np. moduł równoważenia obciążenia będzie mieć inne [ograniczenia](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits) wziąć pod uwagę. Obecnie maksymalnie 50 adresy IP na węzeł zostały sprawdzone i sprawdzone stabilne. 
>

## <a name="set-up-open-networking-mode"></a>Konfigurowanie Otwórz tryb sieci

1. Konfigurowanie szablonu usługi Azure Resource Manager. W **element fabricSettings** sekcji zasobów klastra, Włącz usługi DNS i adresów IP dostawcy: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Skonfiguruj profil sieci części zasobu zestawu skalowania maszyn wirtualnych. Dzięki temu wiele adresów IP, należy skonfigurować na każdym węźle klastra. Poniższy przykład ustawia pięciu adresów IP w każdym węźle klastra systemu Windows/Linux usługi Service Fabric. Masz pięć wystąpień usługi nasłuchuje na porcie w każdym węźle. Aby pięć adresów IP, być dostępny z usługi Azure Load Balancer, należy zarejestrować pięć adresów IP w puli adresów zaplecza modułu równoważenia obciążenia platformy Azure, jak pokazano poniżej.  Należy również dodać zmienne na początku w sekcji zmiennych szablonu.

    Tę sekcję należy dodać do zmiennych:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Tę sekcję należy dodać do zasobu zestawu skalowania maszyn wirtualnych:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Tylko Windows klastrów Skonfiguruj regułę sieciowej grupy zabezpieczeń (NSG) platformy Azure, która otwiera port UDP/53 dla sieci wirtualnej, z następującymi wartościami:

   |Ustawienie |Wartość | |
   | --- | --- | --- |
   |Priorytet |2000 | |
   |Name (Nazwa) |Custom_Dns  | |
   |Element źródłowy |VirtualNetwork | |
   |Element docelowy | VirtualNetwork | |
   |Usługa | DNS (UDP/53) | |
   |Akcja | Zezwalaj  | |
   | | |

4. Określ tryb sieci w manifeście aplikacji dla każdej usługi: `<NetworkConfig NetworkType="Open">`. **Otwórz** sieć trybu wyników w usłudze wprowadzenie dedykowany adres IP. Jeśli tryb nie jest określona, domyślnie Usługa **translatora adresów sieciowych** trybu. W poniższym przykładzie manifest `NodeContainerServicePackage1` i `NodeContainerServicePackage2` usług można każdego nasłuchiwania na tym samym porcie (obie te usługi nasłuchują na `Endpoint1`). Jeśli nie określono tryb otwarcia sieci, `PortBinding` konfiguracji nie może być określony.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Możesz mieszać i dopasowywać różne tryby sieci w usługach w ramach aplikacji w przypadku klastra Windows. Niektóre usługi mogą używać Otwórz tryb, podczas gdy inne osoby w trybie translatora adresów sieciowych. Gdy usługa jest skonfigurowana do używania trybu translatora adresów sieciowych, numer portu, którego nasłuchuje usługa musi być unikatowa.

    >[!NOTE]
    >W klastrach systemu Linux mieszanie tryby sieci dla różnych usług nie jest obsługiwane. 
    >

5. Gdy **Otwórz** wybrania trybu **punktu końcowego** definicji w manifeście usługi jawnie powinien wskazywać pakietu kodu odpowiadający punktu końcowego, nawet jeśli pakiet usługi ma tylko jeden kod pakiet w nim. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Dla Windows ponowne uruchomienie maszyny Wirtualnej spowoduje, że otwartej sieci do odtworzenia. Ma to rozwiązać problemu bazowego w stos sieciowy. Domyślnym zachowaniem jest ponownie utworzyć w sieci. Jeśli to zachowanie musi być wyłączona, następującą konfigurację można następuje uaktualnienie konfiguracji.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Kolejne kroki
* [Informacje o modelu aplikacji usługi Service Fabric](service-fabric-application-model.md)
* [Dowiedz się więcej o zasoby manifestu usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Wdrażanie kontenera Windows w usłudze Service Fabric w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w usłudze Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)
