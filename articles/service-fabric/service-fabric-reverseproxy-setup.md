---
title: Konfigurowanie odwrotnego serwera proxy usługi Azure Service
description: Dowiedz się, jak skonfigurować i skonfigurować usługę odwrotnego serwera proxy dla aplikacji sieci szkieletowej usług Azure.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 6e3edb0fe238dcaddb7d99cc68660591f081581c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476676"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Konfigurowanie i konfigurowanie odwrotnego serwera proxy w sieci szkieletowej usług Azure
Reverse proxy to opcjonalna usługa sieci szkieletowej usług Azure, która pomaga mikrousług uruchomionych w klastrze sieci szkieletowej usług odnajdywać i komunikować się z innymi usługami, które mają punkty końcowe http. Aby dowiedzieć się więcej, zobacz [Odwracanie serwera proxy w sieci szkieletowej usług Azure](service-fabric-reverseproxy.md). W tym artykule pokazano, jak skonfigurować i skonfigurować odwrotny serwer proxy w klastrze. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Włączanie odwrotnego serwera proxy przy użyciu witryny Azure portal

Usługa Azure portal udostępnia opcję włączania odwrotnego serwera proxy podczas tworzenia nowego klastra sieci szkieletowej usług. Nie można uaktualnić istniejącego klastra, aby używać odwrotnego serwera proxy za pośrednictwem portalu. 

Aby skonfigurować odwrotny serwer proxy podczas [tworzenia klastra przy użyciu witryny Azure Portal,](./service-fabric-cluster-creation-via-portal.md)upewnij się, że wykonujesz następujące czynności:

1. W **kroku 2: Konfiguracja klastra**, w obszarze **Konfiguracja typu węzła**wybierz **pozycję Włącz odwrotny serwer proxy**.

   ![Włączanie odwrotnego serwera proxy w portalu](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Opcjonalnie) Aby skonfigurować bezpieczny serwer proxy wsteczny, należy skonfigurować certyfikat TLS/SSL. W **kroku 3: Zabezpieczenia**, na **Konfigurowanie ustawień zabezpieczeń klastra**, w obszarze **Typ konfiguracji**, wybierz **niestandardowe**. Następnie w obszarze **Odwrócony certyfikat SSL serwera proxy**wybierz pozycję **Dołącz certyfikat SSL dla odwrotnego serwera proxy** i wprowadź szczegóły certyfikatu.

   ![Konfigurowanie bezpiecznego serwera proxy wstecznego w portalu](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Jeśli podczas tworzenia klastra nie można skonfigurować odwrotnego serwera proxy z certyfikatem, można to zrobić później za pomocą szablonu Menedżera zasobów dla grupy zasobów klastra. Aby dowiedzieć się więcej, zobacz [Włączanie odwrotnego serwera proxy za pomocą szablonów usługi Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Włączanie odwrotnego serwera proxy za pomocą szablonów usługi Azure Resource Manager

W przypadku klastrów na platformie Azure można użyć szablonu usługi Azure Resource Manager, aby włączyć odwrotny serwer proxy w sieci szkieletowej usług. Można włączyć odwrotny serwer proxy podczas tworzenia klastra lub włączyć go, aktualizując klaster w późniejszym czasie. 

W przypadku nowego klastra można [utworzyć niestandardowy szablon Menedżera zasobów](service-fabric-cluster-creation-via-arm.md) lub przykładowy szablon. 

Przykładowe szablony Menedżera zasobów, które mogą pomóc w skonfigurowaniu bezpiecznego serwera proxy wstecznego dla klastra platformy Azure, można znaleźć w [przykładowych szablonach bezpiecznego serwera proxy wstecznego](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) w usłudze GitHub. Instrukcje i szablony używane do konfigurowania bezpiecznego serwera proxy odwrotnego https za pomocą certyfikatu i obsługi narzucania certyfikatu można znaleźć w temacie [Konfigurowanie serwera proxy odwrotnego HTTPS w bezpiecznym klastrze](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) w pliku README.

W przypadku istniejącego klastra można wyeksportować szablon Menedżera zasobów dla grupy zasobów klastra za pomocą [portalu Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli)

Po szablonie Menedżera zasobów można włączyć odwrotny serwer proxy, wykonując następujące czynności:

1. Zdefiniuj port dla odwrotnego serwera proxy w [sekcji Parametry](../azure-resource-manager/templates/template-syntax.md) szablonu.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Określ port dla każdego z obiektów typu węzła w [sekcji Typ zasobu](../azure-resource-manager/templates/template-syntax.md) [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) .

    Port jest identyfikowany przez nazwę parametru, reverseProxyEndpointPort.

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
3. Aby skonfigurować certyfikaty TLS/SSL na porcie dla odwrotnego serwera proxy, dodaj certyfikat do właściwości ***reverseProxyCertificate*** w [sekcji Typ zasobu](../resource-group-authoring-templates.md) **Microsoft.ServiceFabric/clusters** .

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Obsługa certyfikatu odwrotnego serwera proxy innego niż certyfikat klastra
 Jeśli odwrócony certyfikat serwera proxy różni się od certyfikatu, który zabezpiecza klaster, wcześniej określony certyfikat powinien zostać zainstalowany na maszynie wirtualnej i dodany do listy kontroli dostępu (ACL), aby sieć szkieletowa usług mogła uzyskać do niego dostęp. Można to zrobić w [sekcji Typ zasobu](../resource-group-authoring-templates.md) [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) . Aby instalacja, dodaj certyfikat do osProfile. Sekcja rozszerzenia szablonu może zaktualizować certyfikat w acl.

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
> W przypadku używania certyfikatów różniących się od certyfikatu klastra w celu włączenia odwrotnego serwera proxy w istniejącym klastrze należy zainstalować odwrócony certyfikat serwera proxy i zaktualizować listy ACL w klastrze przed włączeniem odwrotnego serwera proxy. Ukończ wdrożenie [szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) przy użyciu ustawień wymienionych wcześniej przed rozpoczęciem wdrażania, aby włączyć odwrotny serwer proxy w krokach 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Włączanie odwrotnego serwera proxy w klastrach autonomicznych

W przypadku klastrów autonomicznych włączysz odwrotny serwer proxy w pliku ClusterConfig.json. Można włączyć odwrotny serwer proxy podczas tworzenia klastra lub uaktualniania konfiguracji dla istniejącego klastra. Aby dowiedzieć się więcej o ustawieniach dostępnych w plikach ClusterConfig.json, zobacz [Autonomiczne ustawienia klastra](./service-fabric-cluster-manifest.md).

W poniższych krokach przedstawiono ustawienia używane do włączania odwrotnego serwera proxy i, opcjonalnie, aby zabezpieczyć odwrotny serwer proxy za pomocą certyfikatu X.509. 

1. Aby włączyć odwrotny serwer proxy, ustaw wartość **reverseProxyEndpointPort** dla typu węzła w obszarze **właściwości** w konfiguracji klastra. Następujący JSON pokazuje ustawienie odwrotnego portu punktu końcowego serwera proxy na 19081 dla węzłów o typie "NodeType0":

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
2. (Opcjonalnie) Aby uzyskać bezpieczny odwrotny serwer proxy, skonfiguruj certyfikat w sekcji **zabezpieczeń** w obszarze **Właściwości**. 
   - W środowisku deweloperskim lub testowym można użyć ustawienia **ReverseProxyCertificate:**

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
   - W środowisku produkcyjnym zalecane jest ustawienie **ReverseProxyCertificateCommonNames:**

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

   Aby dowiedzieć się więcej na temat konfigurowania certyfikatów autonomicznego klastra i zarządzania nimi, a także więcej szczegółów dotyczących konfigurowania certyfikatów używanych do zabezpieczania odwrotnego serwera proxy, zobacz [Zabezpieczenia oparte na certyfikatach X509](./service-fabric-windows-cluster-x509-security.md).

Po zmodyfikowaniu pliku ClusterConfig.json w celu włączenia odwrotnego serwera proxy postępuj zgodnie z instrukcjami w [uaktualnieniu konfiguracji klastra,](service-fabric-cluster-config-upgrade-windows-server.md) aby wypchnąć zmiany do klastra.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Uwidacznianie odwrotnego serwera proxy w porcie publicznym za pośrednictwem modułu równoważenia obciążenia platformy Azure

Aby rozwiązać odwrotny serwer proxy spoza klastra platformy Azure, należy skonfigurować reguły równoważenia obciążenia platformy Azure i sondę kondycji platformy Azure dla portu odwrotnego serwera proxy. Te kroki można wykonać przy użyciu witryny Azure portal lub szablonu Menedżera zasobów w dowolnym momencie po utworzeniu klastra. 

> [!WARNING]
> Podczas konfigurowania portu odwrotnego serwera proxy w moduł równoważenia obciążenia wszystkie mikrousługi w klastrze, które udostępniają punkt końcowy HTTP są adresowalne spoza klastra. Oznacza to, że mikrousługi przeznaczone do wewnętrznej może być wykrywalne przez określonego złośliwego użytkownika. Potencjalnie stwarza to poważne luki w zabezpieczeniach, które można wykorzystać; na przykład:
>
> * Złośliwy użytkownik może uruchomić atak typu "odmowa usługi", wielokrotnie wywołując usługę wewnętrzną, która nie ma wystarczająco wzmocnionej powierzchni ataku.
> * Złośliwy użytkownik może dostarczać nieprawidłowo sformułowane pakiety do usługi wewnętrznej, co powoduje niezamierzone zachowanie.
> * Usługa przeznaczona do wewnętrznej może zwracać prywatne lub poufne informacje, które nie są przeznaczone do narażonych na usługi poza klastrem, ujawniając w ten sposób te poufne informacje złośliwemu użytkownikowi. 
>
> Upewnij się, że w pełni zrozumieć i ograniczyć potencjalne konsekwencje zabezpieczeń dla klastra i aplikacji działających na nim, przed upublicznieniem odwrotnego portu proxy. 
>

Jeśli chcesz udostępnić odwrotnej proxy publicznie dla autonomicznego klastra, sposób, w jaki to zrobić będzie zależeć od systemu hosting klastra i wykracza poza zakres tego artykułu. Poprzednie ostrzeżenie dotyczące publicznego ujawniania odwrotnego serwera proxy nadal ma zastosowanie.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Uwidacznianie odwrotnego serwera proxy przy użyciu witryny Azure portal 

1. W witrynie Azure Portal kliknij grupę zasobów dla klastra, a następnie kliknij moduł równoważenia obciążenia klastra.
2. Aby dodać sondę kondycji dla portu odwrotnego serwera proxy, w lewym okienku okna modułu równoważenia obciążenia, w obszarze **USTAWIENIA**kliknij pozycję **Sondy kondycji**. Następnie kliknij przycisk **Dodaj** u góry okna Sondy kondycji i wprowadź szczegóły portu odwrotnego serwera proxy, a następnie kliknij przycisk **OK**. Domyślnie odwrotny port serwera proxy to 19081, chyba że został zmieniony podczas tworzenia klastra.

   ![Konfigurowanie sondy kondycji odwrotnego serwera proxy](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Aby dodać regułę równoważenia obciążenia w celu udostępnienia portu odwrotnego serwera proxy, w lewym okienku okna modułu równoważenia obciążenia w obszarze **USTAWIENIA**kliknij pozycję **Reguły równoważenia obciążenia**. Następnie kliknij przycisk **Dodaj** u góry okna Reguły równoważenia obciążenia i wprowadź szczegóły portu odwrotnego serwera proxy. Upewnij się, że ustawiono wartość **port** na port, na którym ma być odsłonięty serwer proxy wsteczny, wartość **portu wewnętrznej bazy danych** ustawionego po włączeniu odwrotnego serwera proxy, a wartość **sondy kondycji** do sondy kondycji skonfigurowaną w poprzednim kroku. Ustaw inne pola odpowiednio i kliknij przycisk **OK**.

   ![Konfigurowanie reguły równoważenia obciążenia dla odwrotnego serwera proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Udostępnianie odwrotnego serwera proxy za pomocą szablonów Menedżera zasobów

Poniższy JSON odwołuje się do tego samego szablonu, który jest używany w [Enable reverse proxy za pośrednictwem szablonów usługi Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Informacje na temat tworzenia szablonu Menedżera zasobów lub eksportowania szablonu dla istniejącego klastra można znaleźć w tej sekcji dokumentu.  Zmiany są wprowadzane w sekcji [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [Resource type .](../resource-group-authoring-templates.md)

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Dostosowywanie zachowania serwera proxy wstecznego przy użyciu ustawień sieci szkieletowej

Zachowanie odwrotnego serwera proxy można dostosować za pomocą ustawień sieci szkieletowej w szablonie Menedżera zasobów dla klastrów hostowanych na platformie Azure lub w pliku ClusterConfig.json dla klastrów autonomicznych. Ustawienia sterujące zachowaniem odwrotnego serwera proxy znajdują się w sekcji [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) w sekcji **fabricSettings** w sekcji **właściwości klastra.** 

Na przykład można ustawić wartość **DefaultHttpRequestTimeout,** aby ustawić limit czasu dla żądań do odwrotnego serwera proxy do 180 sekund, jak w następującym JSON:

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

Aby uzyskać więcej informacji na temat aktualizowania ustawień sieci szkieletowej dla klastrów platformy Azure, zobacz [Dostosowywanie ustawień klastra przy użyciu szablonów Menedżera zasobów](service-fabric-cluster-config-upgrade-azure.md). W przypadku klastrów autonomicznych zobacz [Dostosowywanie ustawień klastra dla klastrów autonomicznych](service-fabric-cluster-config-upgrade-windows-server.md). 

Kilka ustawień sieci szkieletowej służy do ustanawiania bezpiecznej komunikacji między odwrotnym serwerem proxy a usługami. Aby uzyskać szczegółowe informacje na temat tych ustawień, zobacz [Łączenie się z bezpieczną usługą za pomocą odwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie przekazywania dalej w celu zabezpieczenia usługi HTTP za pomocą odwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Aby uzyskać opcje konfiguracji serwera proxy wstecznego, zobacz [sekcję ApplicationGateway/Http w obszarze Dostosowywanie ustawień klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
