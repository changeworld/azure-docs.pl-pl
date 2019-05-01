---
title: Tworzenie klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować bezpieczny klaster usługi Service Fabric na platformie Azure przy użyciu usługi Azure Resource Manager.  Można utworzyć klastra przy użyciu domyślnego szablonu lub utworzyć własny szablon klastra.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 52623183139be2b8ac6b12d3adca64e72de932d3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125106"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Tworzenie klastra usługi Service Fabric przy użyciu usługi Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Klastra usługi Azure Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych, w których mikrousługi są wdrażania i zarządzania nimi.  Klaster usługi Service Fabric działających na platformie Azure jest zasobem platformy Azure i jest wdrażany za pomocą usługi Azure Resource Manager. W tym artykule opisano wdrażanie zabezpieczonego klastra usługi Service Fabric na platformie Azure przy użyciu usługi Resource Manager. Można użyć szablonu domyślnego klastra lub szablonem niestandardowym.  Jeśli nie masz jeszcze szablonu niestandardowego, możesz to zrobić [Dowiedz się, jak utworzyć](service-fabric-cluster-creation-create-template.md).

Zabezpieczenia klastra jest skonfigurowany, gdy klaster jest pierwszej instalacji i nie można zmienić później. Przed rozpoczęciem konfigurowania klastra, przeczytaj [scenariusze zabezpieczeń klastra usługi Service Fabric][service-fabric-cluster-security]. Na platformie Azure Usługa Service Fabric używa x509 certyfikatów do zabezpieczenia klastra i jego punkty końcowe, uwierzytelnianie klientów i szyfrowania danych. Usługa Azure Active Directory zaleca się zabezpieczenie dostępu do punktów końcowych zarządzania. Usługa Azure AD dzierżawcy i użytkownicy musi zostać utworzona przed utworzeniem klastra.  Aby uzyskać więcej informacji, przeczytaj [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

W przypadku tworzenia klastra produkcyjnego do uruchamiania obciążeń produkcji, zalecamy przeczytanie pierwszy [Lista kontrolna gotowości produkcji](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
W tym artykule umożliwiają moduły wiersza polecenia platformy Azure lub programu powershell usługi Service Fabric RM wdrożenie klastra:

* [Program Azure PowerShell 4.1 i nowsze wersje][azure-powershell]
* [Interfejs wiersza polecenia platformy Azure w wersji 2.0 i nowsze wersje][azure-CLI]

Można znaleźć dokumentację referencyjną usługi Service Fabric moduły w tym miejscu:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [Moduł interfejsu wiersza polecenia az SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Przed uruchomieniem tych poleceń, w tym artykule, najpierw zaloguj się do platformy Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Utwórz nowy klaster przy użyciu certyfikatu z podpisem własnym wygenerowany przez system

Użyj następujących poleceń do utworzenia klastra zabezpieczonego przy użyciu certyfikatu z podpisem własnym wygenerowany przez system. To polecenie ustawia certyfikatu klastra podstawowego, który jest używany do zabezpieczania klastra i skonfigurować dostęp administratora do wykonywania operacji zarządzania przy użyciu tego certyfikatu.  Certyfikaty z podpisem własnym są przydatne do zabezpieczenia klastrów testowych.  Klastrów produkcyjnych powinien zostać zabezpieczony za pomocą certyfikatu od urzędu certyfikacji (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Użyj domyślnego szablonu klastra, który jest dostarczany w module

Użyj następującego polecenia, aby utworzyć klaster szybko, przez określenie minimalnej liczby parametrów, za pomocą domyślnego szablonu.

Szablon, który jest używany jest dostępna w [przykłady szablonów usługi Azure Service Fabric: szablon windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablonu Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Następujące polecenie, można utworzyć albo Windows lub klastrach systemu Linux, należy odpowiednio określić systemu operacyjnego. Polecenia programu PowerShell/interfejsu wiersza polecenia również danych wyjściowych dla certyfikatu w określonym *CertificateOutputFolder*; Jednakże, upewnij się, że folder certyfikat już utworzony. Wykonanie tego polecenia może w innych parametrów oraz przykład jednostki SKU maszyny Wirtualnej.

> [!NOTE]
> Następujące polecenie programu PowerShell działa tylko z programem Azure PowerShell `Az` modułu. Aby sprawdzić bieżącą wersję programu Azure PowerShell Resource Manager w wersji, uruchom następujące polecenie programu PowerShell "Get-Module Az". Postępuj zgodnie z [ten link](/powershell/azure/install-Az-ps) uaktualnienie wersji usługi Azure Resource Manager w programie PowerShell. 
>
>

Wdrażanie klastra przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Wdrażanie klastra przy użyciu wiersza polecenia platformy Azure:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Użyj szablonu niestandardowego

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Dowiedz się, jak [dostosowywania szablonu klastra][customize-your-cluster-template].

Jeśli masz już szablonu niestandardowego, należy dokładnie sprawdzić, czy wszystkie trzy certyfikatu związany następujące nazwy parametrów w szablonie i pliku parametrów i wartości są równe null w następujący sposób:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Wdrażanie klastra przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Wdrażanie klastra przy użyciu wiersza polecenia platformy Azure:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Utwórz nowy klaster przy użyciu certyfikatu X.509

Aby utworzyć klaster, użyj następującego polecenia, jeśli masz certyfikat, którego chcesz użyć do zabezpieczenia klastra przy użyciu.

Jeśli jest to podpisany certyfikat urzędu certyfikacji i ostatecznie przy użyciu również do innych celów, następnie zalecane jest zapewniają oddzielnej grupie zasobów specjalnie dla własnego magazynu kluczy. Zaleca się umieścić usługi key vault w jego własnej grupie zasobów. Ta akcja umożliwia usunięcie grupy zasobów obliczeniowych i magazynu, w tym grupę zasobów, która zawiera Twój klaster usługi Service Fabric, bez utraty kluczy i wpisów tajnych. **Grupy zasobów, która zawiera Twój magazyn kluczy *musi znajdować się w tym samym regionie* jako klaster, który jest używany.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Użyj domyślnego pięcioma węzłami, szablon typu jeden węzeł, który jest dostarczany w module
Szablon, który jest używany jest dostępna w [przykładów dla platformy Azure: Szablon Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablonu Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Wdrażanie klastra przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Wdrażanie klastra przy użyciu wiersza polecenia platformy Azure:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Użyj szablonu niestandardowego klastra
Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Dowiedz się, jak [dostosowywania szablonu klastra][customize-your-cluster-template].

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że wartości i sprawdź, czy wszystkie trzy certyfikatów związane z następujące nazwy parametrów w szablonie i plik parametrów ma wartość null w następujący sposób.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Wdrażanie klastra przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Wdrażanie klastra przy użyciu wiersza polecenia platformy Azure:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Za pomocą wskaźnika do wpisu tajnego przekazany do magazynu kluczy

Aby użyć istniejącego magazynu kluczy, magazynu kluczy musi być [włączony do wdrożenia](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) umożliwia dostawcy zasobów obliczeniowych, uzyskiwanie certyfikatów i zainstaluj go na węzłach klastra.

Wdrażanie klastra przy użyciu programu PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Wdrażanie klastra przy użyciu wiersza polecenia platformy Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz działające na platformie Azure zabezpieczonego klastra. Następnie [połączenia z klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak [Zarządzanie wpisami tajnymi aplikacji](service-fabric-application-secret-management.md).

Składnię JSON i właściwości, aby użyć szablonu, zobacz [odwołanie do szablonu Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
