---
title: Tworzenie klastra usługi Service Fabric z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak wdrożyć klaster Windows usługi Service Fabric w sieci wirtualnej platformy Azure i sieciowej grupy zabezpieczeń przy użyciu programu PowerShell.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 5ef143fe2021a9f705bf61b579e8251b2946b042
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668096"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Samouczek: Wdrażanie klastra usługi Service Fabric z systemem Windows w sieci wirtualnej platformy Azure

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się, jak wdrożyć klaster usługi Azure Service Fabric z systemem Windows do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) i [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md) przy użyciu programu PowerShell i szablonu. Po zakończeniu będziesz mieć klastra działającego w chmurze, do której można wdrażać aplikacje. Aby utworzyć klastra z systemem Linux, która korzysta z wiersza polecenia platformy Azure, zobacz [tworzenie bezpiecznego klastra z systemem Linux na platformie Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

W tym samouczku opisano scenariusz produkcyjny. Jeśli ma zostać utworzony mniejszy klaster do celów testowych, zobacz [Tworzenie klastra testowego](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu programu PowerShell
> * Tworzenie magazynu kluczy i przekazywanie certyfikatu
> * Konfigurowanie uwierzytelniania usługi Azure Active Directory
> * Konfiguruj zbieranie diagnostyki
> * Konfigurowanie usługi bazy danych EventStore
> * Konfigurowanie dzienników usługi Azure Monitor
> * Tworzenie bezpiecznego klastra usługi Service Fabric w programie Azure PowerShell
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
> * Usuwanie klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego klastra na platformie Azure
> * [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [zestawu SDK usługi Service Fabric i moduł programu PowerShell](service-fabric-get-started.md).
* Zainstaluj [Azure modułu Powershell w wersji 4.1 lub nowszej](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Zapoznaj się z kluczowymi założeniami programu [klastrów Azure](service-fabric-azure-clusters-overview.md).
* [Planowanie i przygotowanie](service-fabric-cluster-azure-deployment-preparation.md) wdrożenia klastra produkcyjnego.

Następujące procedury umożliwiają utworzenie klastra usługi Service Fabric z siedmioma węzłami. Użyj [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) można obliczyć koszt działania klastra usługi Service Fabric na platformie Azure.

## <a name="download-and-explore-the-template"></a>Pobieranie i eksplorowanie szablonu

Pobierz poniższe pliki szablonu usługi Azure Resource Manager:

* [azuredeploy.JSON][template]
* [azuredeploy.parameters.JSON][parameters]

Ten szablon umożliwia wdrożenie zabezpieczonego klastra siedmiu maszyn wirtualnych z trzema typami węzła w sieci wirtualnej i sieciowej grupie zabezpieczeń.  Inne przykładowe szablony można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [Azuredeploy.json] [ template] wdraża liczby zasobów, w tym następujące czynności.

### <a name="service-fabric-cluster"></a>Klaster usługi Service Fabric

W zasobie **Microsoft.ServiceFabric/clusters** skonfigurowano klaster systemu Windows o następujących właściwościach:

* Trzy typy węzłów.
* Pięć węzłów typu węzła podstawowego (z możliwością konfiguracji za pomocą parametrów szablonu) i jednego węzła w każdego typu dwoma węzłami.
* System operacyjny: Windows Server 2016 Datacenter z kontenerami (z możliwością konfiguracji za pomocą parametrów szablonu).
* Zabezpieczenie przy użyciu certyfikatu (z możliwością konfiguracji za pomocą parametrów szablonu).
* [Odwrotny serwer proxy](service-fabric-reverseproxy.md) jest włączona.
* [Usługa DNS](service-fabric-dnsservice.md) jest włączona.
* [Poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) : brązowy (z możliwością konfiguracji za pomocą parametrów szablonu).
* [Poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) : srebrny (z możliwością konfiguracji za pomocą parametrów szablonu).
* punkt końcowy połączenia klienta: 19000 (z możliwością konfiguracji za pomocą parametrów szablonu).
* Punkt końcowy bramy protokołu HTTP: 19080 (z możliwością konfiguracji za pomocą parametrów szablonu).

### <a name="azure-load-balancer"></a>Azure Load Balancer

W **Microsoft.Network/loadBalancers** zasobów, moduł równoważenia obciążenia jest skonfigurowany. Sondy oraz reguły skonfigurowano pod kątem następujących portów:

* punkt końcowy połączenia klienta: 19000
* Punkt końcowy bramy protokołu HTTP: 19080
* port aplikacji: 80
* port aplikacji: 443
* Odwrotny serwer proxy usługi Service Fabric: 19081

Jeśli potrzebne są inne porty aplikacji, musisz dostosować **Microsoft.Network/loadBalancers** zasobów i **Microsoft.Network/networkSecurityGroups** zasobów, aby zezwolić na ruch przychodzący.

### <a name="virtual-network-subnet-and-network-security-group"></a>Sieć wirtualna, podsieć i sieciowa grupa zabezpieczeń

Nazwy sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń są deklarowane w parametrach szablonu. Przestrzenie adresowe sieci wirtualnej i podsieci również są deklarowane w parametrach szablonu i skonfigurowane w zasobie **Microsoft.Network/virtualNetworks**:

* Przestrzeń adresowa sieci wirtualnej: 172.16.0.0/20
* Przestrzeń adresowa podsieci usługi Service Fabric: 172.16.2.0/23

Poniższe reguły ruchu przychodzącego są włączone w zasobie **Microsoft.Network/networkSecurityGroups**. Wartości portów można zmienić, modyfikując zmienne szablonu.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Zakres portów efemerycznych: od 49152 do 65534 (wymaganych jest co najmniej 256 portów).
* Porty do użytku aplikacji: 80 i 443
* Zakres portów aplikacji: od 49152 do 65534 (używane do komunikacji między usługami. Inne nie są otwarte porty w module równoważenia obciążenia).
* Wszystkie pozostałe porty są zablokowane

Jeśli potrzebne są inne porty aplikacji, musisz dostosować **Microsoft.Network/loadBalancers** zasobów i **Microsoft.Network/networkSecurityGroups** zasobów, aby zezwolić na ruch przychodzący.

### <a name="windows-defender"></a>Windows Defender
Domyślnie [programu antywirusowego Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) jest zainstalowane i działające w systemie Windows Server 2016. Interfejs użytkownika jest instalowany domyślnie w niektórych jednostkach SKU, ale nie jest wymagane. Dla każdego typu węzła/zestawu skalowania maszyn wirtualnych zadeklarowanego w szablonie [rozszerzenie Azure VM Antimalware](/azure/virtual-machines/extensions/iaas-antimalware-windows) służy do wykluczania katalogów i procesów usługi Service Fabric:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Ustawianie parametrów szablonu

Plik parametrów [azuredeploy.parameters.json][parameters] deklaruje wiele wartości służących do wdrażania klastra i skojarzonych zasobów. Poniżej przedstawiono parametry, aby zmodyfikować dla danego wdrożenia:

**Parametr** | **Przykładowa wartość** | **Uwagi** 
|---|---|---|
|adminUserName|vmadmin| Nazwa użytkownika będącego administratorem maszyn wirtualnych klastra. [Wymagania dotyczące nazwy użytkownika, dla maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Haslo#1234| Hasło administratora maszyn wirtualnych klastra. [Wymagania dotyczące hasła dla maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mojklastersf123| Nazwa klastra. Może zawierać tylko litery i cyfry. Długość powinna wynosić od 3 do 23 znaków.|
|location|southcentralus| Lokalizacja klastra. |
|certificateThumbprint|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość odcisku palca SHA1 certyfikatu. Na przykład „6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta. </p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź adres URL certyfikatu. Na przykład "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość magazynu źródłowego. Na przykład „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Konfigurowanie uwierzytelniania klienta za pomocą usługi Azure Active Directory
W przypadku klastrów usługi Service Fabric wdrożonych w sieci publicznej hostowanej na platformie Azure zalecenia dla wzajemnego uwierzytelniania klienta i węzła są następujące:
* Usługa Azure Active Directory dla tożsamości klienta.
* Użyj certyfikatu tożsamości serwera i szyfrowania SSL dla komunikacji HTTP.

Konfigurowanie usługi Azure Active Directory (Azure AD) do uwierzytelniania klientów dla klastra usługi Service Fabric, należy wykonać przed [tworzenia klastra](#createvaultandcert). Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. 

Klaster usługi Service Fabric udostępnia kilka punktów wejścia dla swoich funkcji zarządzania, w tym internetowe narzędzie [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) i [program Visual Studio](service-fabric-manage-application-in-visual-studio.md). W związku z tym utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną.  Po utworzeniu aplikacji przypiszesz użytkowników do ról tylko do odczytu i administratora.

> [!NOTE]
> Przed utworzeniem klastra musisz wykonać następujące kroki. Ponieważ skrypty oczekują określenia nazw klastra i punktów końcowych, wartości powinny być zaplanowane i inne od wartości już utworzonych.

W tym artykule przyjęto założenie, że utworzono już dzierżawę. Jeśli jeszcze tego nie, zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Aby uprościć etapy konfigurowania usługi Azure AD z klastrem usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell. [Pobierz skrypty](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) na komputer.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Tworzenie aplikacji usługi Azure AD i przypisywanie ról do użytkowników
Utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną. Po utworzeniu aplikacji ma reprezentować klaster, należy przypisać użytkownikom [role obsługiwane przez usługę Service Fabric](service-fabric-cluster-security-roles.md): tylko do odczytu i administratora.

Uruchom skrypt `SetupApplications.ps1` i podaj jako parametry identyfikator dzierżawy, nazwę klastra i adres URL odpowiedzi aplikacji internetowej. Określ nazwy użytkowników i hasła dla użytkowników. Na przykład:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Chmur krajowych (na przykład Azure dla instytucji rządowych, chińska wersja platformy Azure, Azure [Niemcy]), określ `-Location` parametru.

Wartość *TenantId*, czyli identyfikator katalogu, możesz znaleźć w witrynie [Azure Portal](https://portal.azure.com). Wybierz **usługi Azure Active Directory** > **właściwości** i skopiuj **identyfikator katalogu** wartość.

Wartość *ClusterName* służy jako prefiks aplikacji usługi Azure AD tworzonych przez skrypt. Nie musi dokładnie odpowiadać rzeczywista nazwa klastra. Tylko ułatwia do mapowania artefaktów w usłudze Azure AD do klastra usługi Service Fabric w użyciu.

Wartość *WebApplicationReplyUrl* to domyślny punkt końcowy zwracany przez usługę Azure AD do użytkowników, gdy zakończą logowanie. Ustaw ten punkt końcowy jako punkt końcowy narzędzia Service Fabric Explorer dla klastra. Domyślnie to:

https://&lt;domena_klastra&gt;:19080/Explorer

Zostanie wyświetlony monit Zaloguj się do konta z uprawnieniami administratora dla dzierżawy usługi Azure AD. Po zalogowaniu skrypt utworzy aplikacje internetową i natywną mające reprezentować klaster usługi Service Fabric. W aplikacjach dzierżawcy w [witryny Azure portal](https://portal.azure.com), powinny zostać wyświetlone dwa nowe wpisy:

   * *NazwaKlastra*\_Cluster
   * *NazwaKlastra*\_Client

Skrypt wyświetla plik JSON wymagany przez szablon usługi Resource Manager podczas tworzenia klastra, dzięki czemu jest dobrym rozwiązaniem nie zamykaj okna programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Dodawanie konfiguracji usługi Azure AD do użycia na potrzeby dostępu klientów
W pliku [azuredeploy.json][template] skonfiguruj usługę Azure AD w sekcji **Microsoft.ServiceFabric/clusters**. Dodaj parametry identyfikatora dzierżawy, identyfikatora aplikacji klastra i identyfikatora aplikacji klienckiej.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Dodaj wartości parametrów do pliku parametrów [azuredeploy.parameters.json][parameters]. Na przykład:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Konfiguruj zbieranie diagnostyki w klastrze
Po uruchomieniu klastra usługi Service Fabric to dobry pomysł, aby zbierać dzienniki z wszystkimi węzłami w centralnej lokalizacji. Posiadanie dzienniki w centralnej lokalizacji, ułatwiają analizowanie i rozwiązywanie problemów w klastrze lub problemy w aplikacji i usług działających w klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia diagnostyki platformy Azure (WAD), przekazuje dzienniki do usługi Azure Storage, która ma również możliwość przesyłania dzienników do usługi Azure Application Insights lub centrów zdarzeń. Proces zewnętrzny umożliwia również odczytywać zdarzenia z magazynu i umieścić je w analizy produkt platformy, takie jak dzienniki usługi Azure Monitor lub innego rozwiązania do analizowania dziennika.

Jeśli korzystasz z tego samouczka, zbieranie diagnostyki jest już skonfigurowana w [szablonu][template].

W przypadku istniejącego klastra, który nie ma wdrożonych diagnostyki można dodać lub zaktualizować go za pomocą szablonu klastra. Modyfikowanie szablonu usługi Resource Manager, który jest używany do tworzenia istniejącego klastra lub pobrać szablon z poziomu portalu. Zmodyfikuj plik template.json, wykonując następujące czynności:

Dodaj nowy zasób magazynu do sekcji zasobów w szablonie:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Następnie dodaj parametry typu i nazwy konta magazynu do sekcji parametrów szablonu. Zastąp nazwę konta magazynu tekst symbolu zastępczego przejdzie, w tym miejscu o nazwie magazynu konto oczekuje.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Następnie dodaj **IaaSDiagnostics** rozszerzenie tablica rozszerzeń **VirtualMachineProfile** właściwości każdego **Microsoft.Compute/virtualMachineScaleSets** zasób w klastrze.  Jeśli używasz [przykładowy szablon][template], istnieją trzy zestawy skalowania maszyn wirtualnych (po jednym dla każdego typu węzła w klastrze).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Skonfiguruj usługę bazy danych EventStore
Usługa bazy danych EventStore jest opcji monitorowania w usłudze Service Fabric. Bazy danych EventStore zapewnia sposób, aby sprawdzić stan klastra lub obciążeń w danym punkcie w czasie. Bazy danych EventStore jest stanowej usługi Service Fabric, która utrzymuje zdarzenia z klastra. Zdarzenia są udostępniane za pośrednictwem narzędzia Service Fabric Explorer, REST i interfejsów API. Bazy danych EventStore zapytań klastra bezpośrednio po to, aby uzyskać dane diagnostyczne w dowolnej jednostce w klastrze i powinny być używane do pomocy:

* Diagnozuj problemy z tworzenia i testowania lub gdy być może używasz potoku monitorowania
* Upewnij się, że akcje zarządzania, które są tworzone w klastrze są przetwarzane prawidłowo
* Pobierz "migawkę" jak usługi Service Fabric prowadzi interakcję z określonego obiektu



Aby włączyć usługę bazy danych EventStore w klastrze, należy dodać następujące polecenie, aby **element fabricSettings** właściwość **Microsoft.ServiceFabric/clusters** zasobów:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Konfigurowanie usługi Azure Monitor dzienniki klastra

Dzienniki platformy Azure Monitor to nasze zalecenie, aby monitorować zdarzenia poziomu klastra. Aby skonfigurować dzienniki usługi Azure Monitor do monitorowania klastra, musisz mieć [diagnostyki włączone, aby wyświetlić zdarzenia z poziomu klastra](#configure-diagnostics-collection-on-the-cluster).  

Obszaru roboczego musi być podłączony do danych diagnostycznych z klastra.  Te dane dziennika są przechowywane w *applicationDiagnosticsStorageAccountName* konta magazynu, w WADServiceFabric * EventTable WADWindowsEventLogsTable i WADETWEventTable tabel.

Dodawanie obszaru roboczego usługi Azure Log Analytics i Dodawanie rozwiązania do obszaru roboczego:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Następnie dodaj parametry
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Następnie dodaj zmienne:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Dodawanie usługi Log Analytics agent skalowania poszczególnych maszyn wirtualnych w klastrze oraz rozszerzenia łączenia agenta z obszarem roboczym usługi Log Analytics. Umożliwia to zbieranie danych diagnostycznych o kontenerach, aplikacji i monitorowanie wydajności. Dodając ją jako rozszerzenie do zasobu zestawu skalowania maszyny wirtualnej, usługi Azure Resource Manager zapewnia, że pobiera zainstalowany w każdym węźle, nawet podczas skalowania klastra.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Wdrażanie sieci wirtualnej i klastra

Następnym etapem jest skonfigurowanie topologii sieci i wdrożenie klastra usługi Service Fabric. [Azuredeploy.json] [ template] szablon usługi Resource Manager umożliwia utworzenie sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń dla usługi Service Fabric. Szablon pozwala również wdrożyć klaster z włączonymi zabezpieczeniami opartymi na certyfikacie. W przypadku klastrów produkcyjnych jako certyfikatu klastra należy użyć certyfikatu od urzędu certyfikacji. Do zabezpieczenia klastrów testowych może służyć certyfikat z podpisem własnym.

Szablon w tym artykule umożliwia wdrożenie klastra, który używa odcisku palca certyfikatu do identyfikowania certyfikatu klastra. Żadne dwa certyfikaty nie mogą mieć tego samego odcisku palca, co sprawia, że zarządzanie certyfikatami jest trudniejsze. Przełączanie wdrożonego klastra z odciski palców certyfikatu do nazw pospolitych certyfikatów upraszcza zarządzanie certyfikatami. Aby dowiedzieć się, jak zaktualizować klaster pod kątem używania nazwy pospolite certyfikatów dla certyfikatu zarządzania, przeczytaj [klastra zmiany do wspólnego zarządzania nazwę certyfikatu](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Tworzenie klastra przy użyciu istniejącego certyfikatu

Poniższy skrypt wdraża nowy klaster na platformie Azure za pomocą polecenia cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) i szablonu. Polecenie cmdlet tworzy nowy magazyn kluczy na platformie Azure i przekazanie danego certyfikatu.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Tworzenie klastra przy użyciu nowego certyfikatu z podpisem własnym

Poniższy skrypt wdraża nowy klaster na platformie Azure za pomocą polecenia cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) i szablonu. Polecenie cmdlet tworzy nowy magazyn kluczy na platformie Azure, dodaje nowy certyfikat z podpisem własnym do magazynu kluczy i pobiera plik certyfikatu do lokalnie.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Połącz się z klastrem przy użyciu modułu Service Fabric programu PowerShell, instalowane przy użyciu zestawu SDK usługi Service Fabric.  Najpierw zainstaluj certyfikat w magazynie osobistym (Mój) bieżącego użytkownika na komputerze. Uruchom następujące polecenie programu PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Teraz możesz nawiązać połączenie z zabezpieczonym klastrem.

Moduł programu PowerShell dla usługi **Service Fabric** udostępnia wiele poleceń cmdlet służących do zarządzania usługami, aplikacjami i klastrami usługi Service Fabric. Za pomocą polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) nawiąż połączenie z zabezpieczonym klastrem. Szczegóły dotyczące punktu końcowego połączenia i odcisku palca SHA1 certyfikatu można znaleźć w danych wyjściowych poprzedniego kroku.

Jeśli wcześniej skonfigurowano uwierzytelnianie klienta usługi Azure AD, uruchom następujące polecenie: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Jeśli nie skonfigurowano uwierzytelnianie klienta usługi Azure AD, uruchom następujące polecenie:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Sprawdź, czy masz połączenie i że klaster jest w dobrej kondycji za pomocą [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) polecenia cmdlet.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W pozostałych artykułach z tej serii samouczków jest używany klaster, utworzonych przez siebie. Jeśli nie przechodzisz od razu do następnego artykułu, rozważ [usunięcie klastra](service-fabric-cluster-delete.md), aby uniknąć naliczania opłat.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do poniższego samouczka, aby dowiedzieć się, jak skalować klaster.

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu programu PowerShell
> * Tworzenie magazynu kluczy i przekazywanie certyfikatu
> * Konfigurowanie uwierzytelniania usługi Azure Active Directory
> * Konfiguruj zbieranie diagnostyki
> * Konfigurowanie usługi bazy danych EventStore
> * Konfigurowanie dzienników usługi Azure Monitor
> * Tworzenie bezpiecznego klastra usługi Service Fabric w programie Azure PowerShell
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
> * Usuwanie klastra

Następnie przejdź do poniższego samouczka, aby dowiedzieć się, jak monitorowanie klastra.
> [!div class="nextstepaction"]
> [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
