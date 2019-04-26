---
title: Usługa Azure Service Fabric Konfigurowanie zwrotnego serwera proxy | Dokumentacja firmy Microsoft
description: Zrozumienie, jak instalowanie i konfigurowanie zwrotnego serwera proxy usługi Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: v-jamebr
ms.openlocfilehash: 7f1b6f955dd3f59f6c17403b536cf99d666aab08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392998"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Instalowanie i konfigurowanie zwrotnego serwera proxy w usłudze Azure Service Fabric
Zwrotny serwer proxy to opcjonalna usługa Azure Service Fabric, która ułatwia mikrousług działającego w klastrze usługi Service Fabric, odnajdywanie i komunikować się z innymi usługami, które mają punktów końcowych http. Aby dowiedzieć się więcej, zobacz [zwrotny serwer proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy.md). W tym artykule przedstawiono sposób instalowania i konfigurowania zwrotny serwer proxy w klastrze. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Włącz zwrotny serwer proxy przy użyciu witryny Azure portal

Witryna Azure portal udostępnia opcję, aby włączyć zwrotny serwer proxy, podczas tworzenia nowego klastra usługi Service Fabric. Nie można uaktualnić istniejącego klastra, aby użyć zwrotnego serwera proxy za pośrednictwem portalu. 

Aby skonfigurować zwrotnego serwera proxy podczas możesz [Tworzenie klastra przy użyciu witryny Azure portal](./service-fabric-cluster-creation-via-portal.md), upewnij się, wykonaj następujące czynności:

1. W **krok 2: Konfiguracja klastra**w obszarze **konfiguracja typu węzła**, wybierz opcję **Włącz zwrotny serwer proxy**.

   ![Włącz zwrotny serwer proxy w portalu](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Opcjonalnie) Aby skonfigurować bezpieczne zwrotny serwer proxy, należy skonfigurować certyfikat SSL. W **krok 3: Zabezpieczenia**na **Konfiguruj ustawienia zabezpieczeń klastra**w obszarze **typu konfiguracji**, wybierz opcję **niestandardowe**. Następnie w obszarze **certyfikat zwrotnego serwera Proxy protokołu SSL**, wybierz opcję **obejmują certyfikat protokołu SSL dla zwrotnego serwera proxy** i wprowadź informacje dotyczące certyfikatu.

   ![Konfigurowanie bezpiecznego zwrotny serwer proxy w portalu](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Jeśli nie chcesz konfigurować zwrotny serwer proxy przy użyciu certyfikatu podczas tworzenia klastra, możesz zrobić to później przy użyciu szablonu usługi Resource Manager dla grupy zasobów klastra. Aby dowiedzieć się więcej, zobacz [Włącz zwrotny serwer proxy przy użyciu szablonów usługi Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Włącz zwrotny serwer proxy przy użyciu szablonów usługi Azure Resource Manager

W przypadku klastrów na platformie Azure umożliwia szablonu usługi Azure Resource Manager Włącz zwrotny serwer proxy w usłudze Service Fabric. Podczas tworzenia klastra lub ją włączyć, aktualizując klastra w późniejszym czasie, można włączyć zwrotny serwer proxy. 

Dla nowego klastra możesz [utworzyć niestandardowy szablon usługi Resource Manager](service-fabric-cluster-creation-via-arm.md) lub skorzystać z przykładowego szablonu. 

Przykładowe szablony usługi Resource Manager, które mogą pomóc Ci skonfigurować bezpieczne zwrotny serwer proxy w klastrze platformy Azure można znaleźć [bezpiecznego odwrotnego serwera Proxy przykładowe szablony](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) w witrynie GitHub. Zapoznaj się [Konfigurowanie protokołu HTTPS zwrotny serwer Proxy w zabezpieczonym klastrem](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) w pliku README, aby uzyskać instrukcje i szablony służące do konfigurowania bezpiecznej zwrotny serwer proxy przy użyciu certyfikatu i obsługiwać Przerzucanie certyfikatów.

W przypadku istniejącego klastra, możesz wyeksportować szablon usługi Resource Manager dla zasobu klastra grupy za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell), lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Po utworzeniu szablonu usługi Resource Manager, należy włączyć zwrotny serwer proxy wykonując następujące kroki:

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
2. Określ numer portu dla każdego z obiektów na element nodetype [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [sekcji typu zasobów](../azure-resource-manager/resource-group-authoring-templates.md).

    Numer portu jest identyfikowane przez nazwę parametru reverseProxyEndpointPort.

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
3. Aby skonfigurować certyfikaty SSL na porcie dla zwrotnego serwera proxy, należy dodać certyfikat do ***reverseProxyCertificate*** właściwość **Microsoft.ServiceFabric/clusters** [zasobów Wpisz sekcji](../resource-group-authoring-templates.md).

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Obsługa certyfikat zwrotnego serwera proxy, który jest inny niż certyfikat klastra
 Jeśli certyfikat zwrotnego serwera proxy jest inny niż certyfikat, który zabezpiecza klastra, następnie wcześniej określonego certyfikatu powinny być zainstalowane na maszynie wirtualnej i dodane do listy kontroli dostępu (ACL), tak aby usługi Service Fabric do niego dostęp. Można to zrobić [ **Microsoft.Compute/virtualMachineScaleSets** ](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [sekcji typu zasobów](../resource-group-authoring-templates.md). Do instalacji należy dodać certyfikat do elementu osProfile. Rozszerzenie części szablonu można zaktualizować certyfikatu na liście ACL.

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
> Korzystając z certyfikatów, które różnią się od certyfikatu klastra, aby włączyć zwrotny serwer proxy w istniejącym klastrze, zainstaluj certyfikat zwrotnego serwera proxy i aktualizowanie listy ACL w klastrze, przed włączeniem zwrotnego serwera proxy. Wykonaj [szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) wdrożenia przy użyciu ustawień wymienionych wcześniej przed rozpoczęciem wdrażania, aby włączyć zwrotny serwer proxy w kroki 1 – 3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Włącz zwrotny serwer proxy na autonomicznych klastrów

W przypadku klastrów autonomicznych możesz włączyć zwrotny serwer proxy w pliku ClusterConfig.json. Można włączyć zwrotny serwer proxy, podczas tworzenia klastra lub zmodernizowanie konfiguracji dla istniejącego klastra. Aby dowiedzieć się więcej na temat ustawień dostępnych w plikach ClusterConfig.json, zobacz [ustawienia klastra autonomicznego](./service-fabric-cluster-manifest.md).

Poniższe kroki pokazują, ustawienia Aby użyć, aby włączyć zwrotny serwer proxy i, opcjonalnie, aby zabezpieczyć zwrotny serwer proxy przy użyciu certyfikatu X.509. 

1. Aby włączyć zwrotny serwer proxy, należy ustawić **reverseProxyEndpointPort** wartość dla typu węzła, w obszarze **właściwości** w konfiguracji klastra. Następujące dane JSON przedstawiono ustawienie zwrotny serwer proxy port punktu końcowego 19081 węzłów z typem "NodeType0":

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Opcjonalnie) Dla bezpiecznych zwrotny serwer proxy, należy skonfigurować certyfikat w **zabezpieczeń** sekcji **właściwości**. 
   - W przypadku środowiska deweloperskie lub testowe, można użyć **ReverseProxyCertificate** ustawienia:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - W środowisku produkcyjnym **ReverseProxyCertificateCommonNames** ustawienie jest zalecane:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Aby dowiedzieć się więcej na temat konfigurowania i zarządzania certyfikatami dla klastrów autonomicznych, jak również szczegółowe informacje o konfigurowaniu certyfikatów służących do zabezpieczania zwrotny serwer proxy, zobacz [X509 zabezpieczenia oparte na certyfikatach](./service-fabric-windows-cluster-x509-security.md).

Po zmodyfikowaniu pliku ClusterConfig.json, aby włączyć zwrotny serwer proxy, postępuj zgodnie z instrukcjami [Uaktualnij konfigurację klastra](service-fabric-cluster-config-upgrade-windows-server.md) wypychanie zmian do klastra.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Udostępnianie zwrotny serwer proxy na publicznym porcie za pośrednictwem usługi Azure Load Balancer

Aby adres zwrotny serwer proxy z poza klastrze platformy Azure, należy skonfigurować zasady usługi Azure Load Balancer i Azure kondycji sondować port zwrotnego serwera proxy. Te kroki można wykonać przy użyciu witryny Azure portal lub szablonu usługi Resource Manager, w dowolnym momencie po utworzeniu klastra. 

> [!WARNING]
> Po skonfigurowaniu port zwrotnego serwera proxy w module równoważenia obciążenia, wszystkie mikrousług w klastrze, które ujawniają punkt końcowy HTTP adresowane z poza klastrem. Oznacza to, że mikrousług należy traktować jako wewnętrzny mogą być wykrywalny przez złośliwego użytkownika określone. Potencjalnie stwarza poważne luki w zabezpieczeniach, które mogą być wykorzystane; na przykład:
>
> * Złośliwy użytkownik będą mogły uruchamiać wielokrotnie, wywołując wewnętrzna usługa, która nie ma wystarczająco ataku typu "odmowa usługi".
> * Złośliwy użytkownik może dostarczyć źle sformułowane pakiety do wewnętrzna usługa skutkuje niezamierzone zachowanie.
> * Usługi, należy traktować jako wewnętrzny mogą zwracać prywatne lub poufne informacje, nie mają być udostępniana dla usług poza klastrem, w związku z tym udostępnianie danych poufnych na złośliwy użytkownik. 
>
> Upewnij się, w pełni zrozumieć i eliminowanie potencjalnych zagadnienia zabezpieczeń dla klastra i aplikacje działające na, zanim można upublicznić port zwrotnego serwera proxy. 
>

Jeśli chcesz udostępnić zwrotny serwer proxy publicznie dla klastra autonomicznego, sposób, w którym możesz to zrobić będzie zależeć od system obsługujący klastra i wykracza poza zakres tego artykułu. Poprzedni ostrzeżenie o udostępnianie zwrotny serwer proxy publicznie, jednak nadal obowiązuje ograniczenie.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Udostępnianie zwrotny serwer proxy, za pomocą witryny Azure portal 

1. W witrynie Azure portal kliknij grupę zasobów dla klastra, a następnie kliknij usługę równoważenia obciążenia dla klastra.
2. Aby dodać kondycji sondę port zwrotnego serwera proxy w okienku po lewej stronie okna usługi równoważenia obciążenia w sekcji **ustawienia**, kliknij przycisk **sondy kondycji**. Następnie kliknij przycisk **Dodaj** w górnej części kondycji sondy okna i wprowadź szczegóły port zwrotnego serwera proxy, a następnie kliknij przycisk **OK**. Domyślnie port zwrotnego serwera proxy jest 19081, o ile nie został zmieniony po utworzeniu klastra.

   ![Konfigurowanie sondy kondycji zwrotny serwer proxy](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Aby dodać regułę modułu równoważenia obciążenia, aby uwidocznić port zwrotnego serwera proxy, w okienku po lewej stronie okna usługi równoważenia obciążenia, w obszarze **ustawienia**, kliknij przycisk **reguły równoważenia obciążenia**. Następnie kliknij przycisk **Dodaj** w górnej części obciążenia równoważenia reguł, okna i wprowadź szczegóły port zwrotnego serwera proxy. Upewnij się, możesz ustawić **portu** wartość portu używanego przez zwrotny serwer proxy, widoczne, **port zaplecza** wartość ustawiony podczas włączania zwrotnego serwera proxy, port i **sondy kondycji** wartość na sondę kondycji, które skonfigurowano w poprzednim kroku. Ustaw inne pola, ponieważ odpowiednie, a następnie kliknij przycisk **OK**.

   ![Skonfiguruj reguły modułu równoważenia obciążenia dla zwrotnego serwera proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Udostępnianie zwrotny serwer proxy przy użyciu szablonów usługi Resource Manager

Następujące dane JSON odwołuje się do tego samego szablonu, który jest używany w [Włącz zwrotny serwer proxy przy użyciu szablonów usługi Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Zapoznaj się z tej części dokumentu, aby uzyskać informacje o tym, jak utworzyć szablon usługi Resource Manager lub wyeksportować szablon do istniejącego klastra.  Zmiany zostaną wprowadzone do [ **Microsoft.Network/loadBalancers** ](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [sekcji typu zasobów](../resource-group-authoring-templates.md).

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Dostosowywanie zachowania dotyczącego zwrotny serwer proxy przy użyciu ustawień sieci szkieletowej

Można dostosować zachowanie zwrotny serwer proxy przy użyciu ustawień sieci szkieletowej w szablonie usługi Resource Manager w taki sposób, w przypadku klastrów hostowanych na platformie Azure lub w pliku ClusterConfig.json dla autonomicznych klastrów. Ustawienia kontrolujące zachowanie zwrotnego serwera proxy znajdują się w [ **bramy ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji **element fabricSettings** klastrawsekcji**właściwości** sekcji. 

Na przykład można ustawić wartość **DefaultHttpRequestTimeout** umożliwiające ustawienie limitu czasu dla żądania zwrotnego serwera proxy do 180 sekund, tak jak w następujące dane JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Aby uzyskać więcej informacji na temat aktualizowania ustawień sieci szkieletowej dla klastrów platformy Azure, zobacz [dostosować ustawienia klastra przy użyciu szablonów usługi Resource Manager](service-fabric-cluster-config-upgrade-azure.md). W przypadku klastrów autonomicznych, zobacz [Dostosuj ustawienia klastra dla autonomicznych klastrów](service-fabric-cluster-config-upgrade-windows-server.md). 

Kilka ustawień sieci szkieletowej są używane nawiązywania bezpiecznej komunikacji między zwrotny serwer proxy i usługami. Aby uzyskać szczegółowe informacje o tych ustawieniach, zobacz [nawiązywanie połączenia z bezpiecznej usłudze przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie funkcji przekazywania danych do bezpiecznej usługi HTTP przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Dla opcji konfiguracji zwrotny serwer proxy, zobacz [sekcji bramy ApplicationGateway/Http w ustawieniach klastra dostosować usługi Service Fabric](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
