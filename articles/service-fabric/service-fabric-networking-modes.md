---
title: Konfigurowanie trybów sieciowych dla usług kontenerowych
description: Dowiedz się, jak skonfigurować różne tryby sieciowe obsługiwane przez sieć szkieletową usług Azure.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: ba1fa92559d39a481008d1dd18036e4232be1bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639806"
---
# <a name="service-fabric-container-networking-modes"></a>Tryby sieci szkieletowej usług kontenerów

Klaster sieci szkieletowej usług Azure dla usług kontenerów domyślnie używa trybu sieciowego **nat.** Gdy więcej niż jedna usługa kontenera nasłuchuje na tym samym porcie i jest używany tryb nat, mogą wystąpić błędy wdrażania. Aby obsługiwać wiele usług kontenerów nasłuchujących na tym samym porcie, sieć szkieletowa usług **oferuje** tryb otwartej sieci (wersje 5.7 i nowsze). W trybie Open każda usługa kontenera ma wewnętrzny, dynamicznie przypisany adres IP, który obsługuje wiele usług nasłuchiwania na tym samym porcie.  

Jeśli masz jedną usługę kontenera ze statycznym punktem końcowym w manifeście usługi, możesz utworzyć i usunąć nowe usługi przy użyciu trybu Otwartego bez błędów wdrażania. Ten sam plik docker-compose.yml może być również używany z mapowaniami portów statycznych do tworzenia wielu usług.

Gdy usługa kontenera zostanie ponownie uruchomiona lub zostanie przeniesiony do innego węzła w klastrze, adres IP zostanie przesunie się. Z tego powodu nie zaleca się używania dynamicznie przypisywany adres IP do odnajdywanie usług kontenera. Tylko usługa nazewnictwa sieci szkieletowej usług lub usługa DNS powinny być używane do odnajdowania usług. 

>[!WARNING]
>Platforma Azure umożliwia łącznie 65 356 adresów IP na sieć wirtualną. Suma liczby węzłów i liczby wystąpień usługi kontenera (korzystających z trybu Otwartego) nie może przekroczyć 65 356 adresów IP w sieci wirtualnej. W przypadku scenariuszy o dużej gęstości zaleca się tryb sieciowy nat. Ponadto inne zależności, takie jak moduł równoważenia obciążenia, będą miały inne [ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) do rozważenia. Obecnie do 50 adresów IP na węzeł zostały przetestowane i sprawdzone stabilne. 
>

## <a name="set-up-open-networking-mode"></a>Konfigurowanie trybu otwartej sieci

1. Konfigurowanie szablonu usługi Azure Resource Manager. W sekcji **fabricSettings** zasobu klastra włącz usługę DNS i dostawcę IP: 

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
    
2. Skonfiguruj sekcję profilu sieciowego zasobu Zestaw skalowania maszyny wirtualnej. Dzięki temu można skonfigurować wiele adresów IP w każdym węźle klastra. W poniższym przykładzie konfiguruje pięć adresów IP na węzeł klastra sieci szkieletowej usług systemu Windows/Linux. Może mieć pięć wystąpień usługi nasłuchiwania na porcie w każdym węźle. Aby pięć adresów IP było dostępnych z modułu Równoważenia obciążenia platformy Azure, zarejestruj pięć adresów IP w puli adresów wewnętrznej bazy danych modułu równoważenia obciążenia platformy Azure, jak pokazano poniżej.  Należy również dodać zmienne do górnej części szablonu w sekcji zmiennych.

    Dodaj tę sekcję do zmiennych:

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
    
    Dodaj tę sekcję do zasobu Zestaw skalowania maszyny wirtualnej:

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
 
3. Tylko w przypadku klastrów systemu Windows skonfiguruj regułę sieciowej grupy zabezpieczeń platformy Azure (NSG), która otwiera port UDP/53 dla sieci wirtualnej z następującymi wartościami:

   |Ustawienie |Wartość | |
   | --- | --- | --- |
   |Priorytet |2000 | |
   |Nazwa |Custom_Dns  | |
   |Element źródłowy |VirtualNetwork | |
   |Element docelowy | VirtualNetwork | |
   |Usługa | DNS (UDP/53) | |
   |Akcja | Zezwalaj  | |
   | | |

4. Określ tryb sieciowy w manifeście `<NetworkConfig NetworkType="Open">`aplikacji dla każdej usługi: . **Tryb** otwartej sieci powoduje uzyskanie przez usługę dedykowanego adresu IP. Jeśli tryb nie jest określony, usługa domyślnie **tryb nat.** W poniższym przykładzie `NodeContainerServicePackage1` `NodeContainerServicePackage2` manifestu usługi mogą nasłuchiwania na `Endpoint1`tym samym porcie (obie usługi są nasłuchiwaniem). Po określeniu trybu `PortBinding` otwartej sieci nie można określić konfiguracji.

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

    Można mieszać i dopasowywanie różnych trybów sieciowych między usługami w aplikacji dla klastra systemu Windows. Niektóre usługi mogą korzystać z trybu Open, podczas gdy inne używają trybu nat. Gdy usługa jest skonfigurowana do korzystania z trybu nat, port, na którym nasłuchuje usługa, musi być unikatowy.

    >[!NOTE]
    >W klastrach systemu Linux mieszanie trybów sieciowych dla różnych usług nie jest obsługiwane. 
    >

5. Po wybraniu trybu **Otwartego** **definicja punktu końcowego** w manifeście usługi powinna jawnie wskazywać pakiet kodu odpowiadający punktowi końcowemu, nawet jeśli pakiet usługi ma tylko jeden pakiet kodu. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. W systemie Windows ponowne uruchomienie maszyny Wirtualnej spowoduje odtworzenie otwartej sieci. Ma to na celu złagodzenie podstawowego problemu w stosie sieciowym. Domyślnym zachowaniem jest ponownetworzenie sieci. Jeśli to zachowanie musi być wyłączone, można użyć następującej konfiguracji, po której następuje uaktualnienie konfiguracji.

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
 
## <a name="next-steps"></a>Następne kroki
* [Informacje o modelu aplikacji usługi Service Fabric](service-fabric-application-model.md)
* [Dowiedz się więcej o zasobach manifestu usługi sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Wdrażanie kontenera systemu Windows w sieci szkieletowej usług w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)
