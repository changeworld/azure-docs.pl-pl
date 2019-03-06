---
title: Tworzenie klastra usługi Service Fabric z systemem Windows na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wdrożyć klaster usługi Service Fabric z systemem Windows w sieci wirtualnej platformy Azure oraz w sieciowej grupie zabezpieczeń za pomocą programu PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 64ba17053179d428f5ef7e5ce9685240bde6665f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822995"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Samouczek: Wdrażanie klastra usługi Service Fabric z systemem Windows w sieci wirtualnej platformy Azure

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się w nim, jak wdrożyć klaster usługi Service Fabric, na którym działa system Windows w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) i [sieciowej grupie zabezpieczeń](../virtual-network/virtual-networks-nsg.md) za pomocą programu PowerShell i szablonu. Po wykonaniu tych czynności powstanie działający w chmurze klaster, w którym można będzie wdrażać aplikacje.  Aby utworzyć klaster z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie bezpiecznego klastra z systemem Linux na platformie Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

W tym samouczku opisano scenariusz produkcyjny.  Jeśli chcesz szybko utworzyć mniejszy klaster do celów testowych, zobacz [Tworzenie klastra testowego](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu programu PowerShell
> * Tworzenie magazynu kluczy i przekazywanie certyfikatu
> * Konfigurowanie uwierzytelniania usługi Azure Active Directory
> * Tworzenie bezpiecznego klastra usługi Service Fabric w programie Azure PowerShell
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
> * Usuwanie klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego klastra na platformie Azure
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [zestaw SDK usługi Service Fabric i moduł programu PowerShell](service-fabric-get-started.md)
* Zainstaluj [moduł Azure PowerShell w wersji 4.1 lub nowszej](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
* Zapoznaj się z podstawowymi pojęciami dotyczącymi [klastrów platformy Azure](service-fabric-azure-clusters-overview.md)

Następujące procedury umożliwiają utworzenie klastra usługi Service Fabric z siedmioma węzłami. Aby obliczyć koszt działania klastra usługi Service Fabric na platformie Azure, skorzystaj z [Kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Pobieranie i eksplorowanie szablonu

Pobierz poniższe pliki szablonu usługi Resource Manager:

* [azuredeploy.JSON][template]
* [azuredeploy.parameters.JSON][parameters]

Ten szablon umożliwia wdrożenie zabezpieczonego klastra siedmiu maszyn wirtualnych z trzema typami węzła w sieci wirtualnej i sieciowej grupie zabezpieczeń.  Inne przykładowe szablony można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates).  Plik [azuredeploy.json][template] umożliwia wdrożenie szeregu zasobów, w tym następujących.

### <a name="service-fabric-cluster"></a>Klaster usługi Service Fabric

W zasobie **Microsoft.ServiceFabric/clusters** skonfigurowano klaster systemu Windows o następujących właściwościach:

* Trzy typy węzła
* Pięć węzłów o podstawowym typie węzła (z możliwością konfiguracji za pomocą parametrów szablonu) i po jednym węźle dwóch pozostałych typów węzła
* System operacyjny: Windows Server 2016 Datacenter z kontenerami (z możliwością konfiguracji za pomocą parametrów szablonu)
* Zabezpieczenie przy użyciu certyfikatu (z możliwością konfiguracji za pomocą parametrów szablonu)
* [Odwrotny serwer proxy](service-fabric-reverseproxy.md) jest włączony
* [Usługa DNS](service-fabric-dnsservice.md) jest włączona
* [Poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster): Brązowy (z możliwością konfiguracji za pomocą parametrów szablonu)
* [Poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster): Srebrny (z możliwością konfiguracji za pomocą parametrów szablonu)
* Punkt końcowy połączenia klienta: 19000 (z możliwością konfiguracji w parametrach szablonu)
* Punkt końcowy bramy protokołu HTTP: 19080 (z możliwością konfiguracji w parametrach szablonu)

### <a name="azure-load-balancer"></a>Moduł równoważenia obciążenia platformy Azure

W zasobie **Microsoft.Network/loadBalancers** skonfigurowano moduł równoważenia obciążenia, a sondy i reguły skonfigurowano dla następujących portów:

* Punkt końcowy połączenia klienta: 19000
* Punkt końcowy bramy protokołu HTTP: 19080
* Port aplikacji: 80
* Port aplikacji: 443
* Odwrotny serwer proxy usługi Service Fabric: 19081

Jeśli będą potrzebne inne porty aplikacji, będzie trzeba dostosować zasoby **Microsoft.Network/loadBalancers** i **Microsoft.Network/networkSecurityGroups**, aby zezwalać na ruch przychodzący.

### <a name="virtual-network-subnet-and-network-security-group"></a>Sieć wirtualna, podsieć i sieciowa grupa zabezpieczeń

Nazwy sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń są deklarowane w parametrach szablonu.  Przestrzenie adresowe sieci wirtualnej i podsieci również są deklarowane w parametrach szablonu i skonfigurowane w zasobie **Microsoft.Network/virtualNetworks**:

* Przestrzeń adresowa sieci wirtualnej: 172.16.0.0/20
* Przestrzeń adresowa podsieci usługi Service Fabric: 172.16.2.0/23

Poniższe reguły ruchu przychodzącego są włączone w zasobie **Microsoft.Network/networkSecurityGroups**. Wartości portów można zmienić, modyfikując zmienne szablonu.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Zakres portów efemerycznych: od 49152 do 65534 (wymaganych jest co najmniej 256 portów)
* Porty do użytku aplikacji: 80 i 443
* Zakres portów aplikacji: od 49152 do 65534 (używane do komunikacji między usługami, ale nie są otwarte w przypadku modułu równoważenia obciążenia)
* Wszystkie pozostałe porty są zablokowane

Jeśli będą potrzebne inne porty aplikacji, będzie trzeba dostosować zasoby **Microsoft.Network/loadBalancers** i **Microsoft.Network/networkSecurityGroups**, aby zezwalać na ruch przychodzący.

### <a name="windows-defender"></a>Windows Defender
Domyślnie [program antywirusowy Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) jest zainstalowany i działa w systemie Windows Server 2016. Interfejs użytkownika jest domyślnie instalowany w przypadku niektórych jednostek SKU, ale nie jest to wymagane.  Dla każdego typu węzła/zestawu skalowania maszyn wirtualnych zadeklarowanego w szablonie [rozszerzenie Azure VM Antimalware](/azure/virtual-machines/extensions/iaas-antimalware-windows) służy do wykluczania katalogów i procesów usługi Service Fabric:

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

Plik parametrów [azuredeploy.parameters.json][parameters] deklaruje wiele wartości służących do wdrażania klastra i skojarzonych zasobów. Niektóre parametry, które być może będzie trzeba zmodyfikować na potrzeby danego wdrożenia:

|Parametr|Przykładowa wartość|Uwagi|
|---|---||
|adminUserName|vmadmin| Nazwa użytkownika będącego administratorem maszyn wirtualnych klastra. [Username requirements for VM (Wymagania dotyczące nazwy użytkownika dla maszyny wirtualnej)](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Haslo#1234| Hasło administratora maszyn wirtualnych klastra. [Password requirements for VM (Wymagania dotyczące hasła dla maszyny wirtualnej)](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mojklastersf123| Nazwa klastra. Może zawierać tylko litery i cyfry. Długość powinna wynosić od 3 do 23 znaków.|
|location|southcentralus| Lokalizacja klastra. |
|certificateThumbprint|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość odcisku palca SHA1 certyfikatu. Na przykład „6190390162C988701DB5676EB81083EA608DCCF3”</p>. |
|certificateUrlValue|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta. </p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź adres URL certyfikatu. Na przykład „https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość magazynu źródłowego. Na przykład „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Konfigurowanie uwierzytelniania klienta za pomocą usługi Azure Active Directory
W przypadku klastrów usługi Service Fabric wdrożonych w sieci publicznej hostowanej na platformie Azure zalecenia dla wzajemnego uwierzytelniania klienta i węzła są następujące:
* Używanie usługi Azure Active Directory na potrzeby określania tożsamości klienta
* Certyfikat na potrzeby potwierdzania tożsamości serwera oraz szyfrowania SSL komunikacji HTTP

Konfigurowanie usługi Azure AD do uwierzytelniania klientów dla klastra usługi Service Fabric musi zostać wykonane przed [utworzeniem klastra](#createvaultandcert).  Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. 

Klaster usługi Service Fabric udostępnia kilka punktów wejścia dla swoich funkcji zarządzania, w tym internetowe narzędzie [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) i [program Visual Studio](service-fabric-manage-application-in-visual-studio.md). W związku z tym utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną.  Po utworzeniu aplikacji przypiszesz użytkowników do ról tylko do odczytu i administratora.

> [!NOTE]
> Przed utworzeniem klastra musisz wykonać następujące kroki. Ponieważ skrypty oczekują określenia nazw klastra i punktów końcowych, wartości powinny być zaplanowane i inne od wartości już utworzonych.

W tym artykule przyjęto założenie, że dzierżawa została już utworzona. Jeśli tak nie jest, zacznij od zapoznania się z artykułem [Jak uzyskać dzierżawę usługi Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Aby uprościć niektóre kroki konfigurowania usługi Azure AD za pomocą klastra usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell. [Pobierz skrypty](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) na komputer.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Tworzenie aplikacji usługi Azure AD i przypisywanie ról do użytkowników
Utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną. Po utworzeniu aplikacji reprezentujących klaster przypiszesz użytkowników do [ról obsługiwanych przez usługę Service Fabric](service-fabric-cluster-security-roles.md): tylko do odczytu i administratora.

Uruchom skrypt `SetupApplications.ps1` i podaj jako parametry identyfikator dzierżawy, nazwę klastra i adres URL odpowiedzi aplikacji internetowej.  Podaj także nazwy użytkowników i ich hasła.  Na przykład:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> W przypadku chmur krajowych (na przykład Azure Government, Azure — Chiny, Azure — Niemcy) należy także określić parametr `-Location`.

Wartość *TenantId*, czyli identyfikator katalogu, możesz znaleźć w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Azure Active Directory -> Właściwości** i skopiuj wartość **Identyfikator katalogu**.

Wartość *ClusterName* służy jako prefiks aplikacji usługi Azure AD tworzonych przez skrypt. Nie musi ona dokładnie pasować do rzeczywistej nazwy klastra. Ma na celu tylko ułatwienie mapowania artefaktów usługi Azure AD do klastra usługi Service Fabric, w którym są używane.

Wartość *WebApplicationReplyUrl* to domyślny punkt końcowy zwracany przez usługę Azure AD do użytkowników, gdy zakończą logowanie. Ustaw ten punkt końcowy jako punkt końcowy narzędzia Service Fabric Explorer dla klastra. Domyślnie to:

https://&lt;domena_klastra&gt;:19080/Explorer

Zostanie wyświetlony monit logowania na konto z uprawnieniami administratora dzierżawy usługi Azure AD. Po zalogowaniu skrypt utworzy aplikacje internetową i natywną mające reprezentować klaster usługi Service Fabric. Jeśli popatrzysz na aplikacje dzierżawy w witrynie [Azure Portal](https://portal.azure.com), powinny być widoczne dwie nowe pozycje:

   * *NazwaKlastra*\_Cluster
   * *NazwaKlastra*\_Client

Skrypt wyświetla kod JSON wymagany przez szablon usługi Azure Resource Manager podczas tworzenia klastra, dlatego warto pozostawić otwarte okno programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Dodawanie konfiguracji usługi Azure AD do użycia na potrzeby dostępu klientów
W pliku [azuredeploy.json][template] skonfiguruj usługę Azure AD w sekcji **Microsoft.ServiceFabric/clusters**.  Dodaj parametry identyfikatora dzierżawy, identyfikatora aplikacji klastra i identyfikatora aplikacji klienckiej.  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
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

Dodaj wartości parametrów do pliku parametrów [azuredeploy.parameters.json][parameters].  Na przykład:

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

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Wdrażanie sieci wirtualnej i klastra

Następnym etapem jest skonfigurowanie topologii sieci i wdrożenie klastra usługi Service Fabric. Plik [azuredeploy.json][template] usługi Resource Manager umożliwia utworzenie sieci wirtualnej, podsieci oraz sieciowej grupy zabezpieczeń dla usługi Service Fabric. Szablon pozwala również wdrożyć klaster z włączonymi zabezpieczeniami opartymi na certyfikacie.  W przypadku klastrów produkcyjnych jako certyfikatu klastra należy używać certyfikatu z urzędu certyfikacji. Do zabezpieczenia klastrów testowych może służyć certyfikat z podpisem własnym.

Szablon w tym artykule umożliwia wdrożenie klastra, który używa odcisku palca certyfikatu do identyfikowania certyfikatu klastra.  Żadne dwa certyfikaty nie mogą mieć tego samego odcisku palca, co sprawia, że zarządzanie certyfikatami jest trudniejsze. Przełączenie wdrożonego klastra z używania odcisków palca certyfikatu na używanie nazw pospolitych certyfikatów sprawia, że zarządzanie certyfikatami jest znacznie prostsze.  Aby dowiedzieć się, jak zaktualizować klaster pod kątem używania nazw pospolitych certyfikatów do zarządzania certyfikatami, przeczytaj artykuł [Modyfikacja klastra pod kątem zarządzania certyfikatami za pomocą nazw pospolitych](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Tworzenie klastra przy użyciu istniejącego certyfikatu

Poniższy skrypt wdraża nowy klaster na platformie Azure za pomocą polecenia cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) i szablonu. Polecenie cmdlet powoduje także utworzenie nowego magazynu kluczy na platformie Azure i przekazanie danego certyfikatu.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Tworzenie klastra przy użyciu nowego certyfikatu z podpisem własnym

Poniższy skrypt wdraża nowy klaster na platformie Azure za pomocą polecenia cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) i szablonu. Polecenie cmdlet tworzy również nowy magazyn kluczy na platformie Azure, dodaje nowy certyfikat z podpisem własnym do magazynu kluczy i pobiera plik certyfikatu do środowiska lokalnego.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Nawiąż połączenie z klastrem za pomocą modułu programu PowerShell dla usługi Service Fabric instalowanego wraz z zestawem SDK usługi Service Fabric.  Najpierw zainstaluj certyfikat w magazynie osobistym (Mój) bieżącego użytkownika na komputerze.  Uruchom następujące polecenie programu PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Teraz możesz przystąpić do nawiązywania połączenia z zabezpieczonym klastrem.

Moduł programu PowerShell dla usługi **Service Fabric** udostępnia wiele poleceń cmdlet służących do zarządzania usługami, aplikacjami i klastrami usługi Service Fabric.  Za pomocą polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) nawiąż połączenie z zabezpieczonym klastrem. Szczegóły dotyczące punktu końcowego połączenia i odcisku palca SHA1 certyfikatu można znaleźć w danych wyjściowych poprzedniego kroku.

Jeśli wcześniej skonfigurowano uwierzytelnianie klienta w usłudze AAD, uruchom następujące polecenie: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Jeśli nie skonfigurowano uwierzytelniania klienta w usłudze AAD, uruchom następujące polecenie:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Za pomocą polecenia cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) sprawdź, czy połączenie zostało nawiązane oraz czy klaster jest w dobrej kondycji.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W pozostałych artykułach w tej serii samouczków jest używany utworzony właśnie klaster. Jeśli nie przechodzisz od razu do następnego artykułu, rozważ [usunięcie klastra](service-fabric-cluster-delete.md), aby uniknąć naliczania opłat.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu programu PowerShell
> * Tworzenie magazynu kluczy i przekazywanie certyfikatu
> * Konfigurowanie uwierzytelniania usługi Azure Active Directory
> * Tworzenie bezpiecznego klastra usługi Service Fabric na platformie Azure przy użyciu programu PowerShell
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
> * Usuwanie klastra

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak skalować klaster.
> [!div class="nextstepaction"]
> [Skalowanie klastra](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
