---
title: Konfigurowanie sieci tryby usługi kontenera platformy Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować różne tryby sieci, które są obsługiwane przez sieć szkieletowa usług Azure.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 869b87b8df3b1f532a33e943e728681b358ed8b4
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287632"
---
# <a name="service-fabric-container-networking-modes"></a>Tryby sieci kontenera sieci szkieletowej usług

Używa usługi klastra usługi sieć szkieletowa usług Azure dla kontenera **nat** sieci trybu domyślnie. Gdy więcej niż jedna usługa kontenera nasłuchuje na ten sam port i jest używany tryb translatora adresów sieciowych, mogą wystąpić błędy wdrażania. Do obsługi wielu usług kontenera nasłuchiwania na tym samym porcie, oferuje usługi sieć szkieletowa **Otwórz** trybu sieciowego (w wersji 5.7 lub nowszej). W trybie Otwórz każda usługa kontenera ma wewnętrznego, dynamicznie przypisać adres IP, który obsługuje wielu usług nasłuchiwania na tym samym porcie.  

Jeśli masz jedną usługę kontenera ze statycznego punktu końcowego w manifeście usługi można tworzyć i usuwać nowych usług za pomocą trybu otwartego bez błędów wdrażania. Ten sam plik docker-compose.yml można również z portu statycznego mapowania do utworzenia wielu usług.

Gdy usługi kontenera uruchamia ponownie lub przenoszone do innego węzła w klastrze, zmiany adresu IP. Z tego powodu nie zaleca się przy użyciu z dynamicznie przydzielonego adresu IP do odnajdywania usługi kontenerów. Dla potrzeb odnajdywania usług należy używać tylko Usługa nazewnictwa sieci szkieletowej usług lub usługi DNS. 

>[!WARNING]
>Azure umożliwia łącznie 4096 adresów IP dla sieci wirtualnej. Suma liczby węzłów i numer wystąpienia usługi kontenera (które korzystają z trybu otwartego) nie może przekraczać 4 096 adresów IP sieci wirtualnej. W przypadku scenariuszy o wysokiej gęstości zalecamy translatora adresów sieciowych sieci trybu.
>

## <a name="set-up-open-networking-mode"></a>Konfigurowanie trybu otwartego sieci

1. Konfigurowanie szablonów usługi Azure Resource Manager. W **fabricSettings** sekcji, włącz usługę DNS i dostawca IP: 

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
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
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

2. Konfigurowanie sekcji profilu sieciowego umożliwiają wielu adresów IP, które mają być skonfigurowane na każdym węźle klastra. Poniższy przykład powoduje ustawienie pięć adresów IP w każdym węźle klastra sieci szkieletowej usług systemu Windows i Linux. Masz pięć wystąpień usługi nasłuchuje na porcie w każdym węźle.

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
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
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
 
3. Tylko klastrów systemu Windows skonfigurować regułę Azure grupy zabezpieczeń sieci (NSG), który zostaje otwarty port UDP i 53 dla sieci wirtualnej z następującymi wartościami:

   |Ustawienie |Wartość | |
   | --- | --- | --- |
   |Priorytet |2000 | |
   |Name (Nazwa) |Custom_Dns  | |
   |Element źródłowy |VirtualNetwork | |
   |Element docelowy | VirtualNetwork | |
   |Usługa | DNS (UDP/53) | |
   |Akcja | Zezwalaj  | |
   | | |

4. Określ tryb sieci w manifeście aplikacji dla każdej usługi: `<NetworkConfig NetworkType="Open">`. **Otwórz** sieci trybu wyniki w usłudze pobierania dedykowany adres IP. Jeśli tryb nie jest określony, domyślnie usługę **nat** tryb. W poniższym przykładzie manifestu `NodeContainerServicePackage1` i `NodeContainerServicePackage2` usług można każdego nasłuchiwania na tym samym porcie (obie te usługi są nasłuchiwanie `Endpoint1`). W przypadku sieci trybu otwartego `PortBinding` konfiguracji nie może zostać określony.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

    Można mieszać i dopasowywać różne tryby sieciowych w usługach w ramach aplikacji w klastrze systemu Windows. Niektóre usługi można użyć trybu otwartego, podczas gdy inne osoby w trybie translatora adresów sieciowych. Gdy usługa jest skonfigurowana do używania trybu translatora adresów sieciowych, port, którego nasłuchuje usługa musi być unikatowa.

    >[!NOTE]
    >W systemie Linux klastrów mieszanie tryby sieci dla różnych usług nie jest obsługiwane. 
    >

5. Gdy **Otwórz** wybrany jest tryb, **punktu końcowego** definicji w manifeście usługi jawnie powinny wskazywać pakietu kodu odpowiadający punktu końcowego, nawet jeśli pakiet usługi ma tylko jeden kod pakiet w nim. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. W systemie Windows ponowne uruchomienie maszyny Wirtualnej spowoduje, że otwartej sieci do odtworzenia. To jest ograniczyć podstawowego problemu w stos sieciowy. Domyślne zachowanie jest odtworzyć sieci. Jeśli to zachowanie musi być wyłączona, następującą konfigurację można następuje uaktualnienie konfiguracji.

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
* [Dowiedz się więcej o zasoby manifestu usługi sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Wdrażanie kontenera systemu Windows w sieci szkieletowej usług w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera Docker sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)
