---
title: Tworzenie klastra usługi Azure Service Fabric na podstawie szablonu | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania bezpiecznego klastra usługi Service Fabric na platformie Azure przy użyciu usługi Azure Resource Manager, usługi Azure Key Vault i Azure Active Directory (Azure AD) do uwierzytelniania klientów.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: aljo
ms.openlocfilehash: cad98954c89c37e57d44abf2af54e903a1b4a740
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504927"
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Tworzenie klastra usługi Service Fabric za pomocą usługi Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ten przewodnik krok po kroku przedstawiono definiowanie bezpiecznego klastra usługi Azure Service Fabric na platformie Azure przy użyciu usługi Azure Resource Manager. Firma Microsoft potwierdza, że artykuł jest długa. Niemniej jednak chyba że znasz już dokładnie z zawartością, upewnij się dokładnie wykonać każdy krok.

Przewodnik obejmuje następujące procedury:

* Kluczowe założenia, które należy wiedzieć przed wdrożeniem klastra usługi Service Fabric.
* Tworzenie klastra na platformie Azure przy użyciu modułów Menedżera zasobów usługi Service Fabric.
* Konfigurowanie usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników wykonywania operacji zarządzania w klastrze.
* Tworzenie szablonu niestandardowego usługi Azure Resource Manager dla klastra i ich wdrażania.

## <a name="key-concepts-to-be-aware-of"></a>Kluczowe założenia wiedzieć
Na platformie Azure, Usługa Service Fabric wymaganiami, która pozwala korzystać z x509 certyfikatu w celu zabezpieczenia klastra i jego punkty końcowe. W usłudze Service Fabric używa się certyfikatów, aby zapewniać uwierzytelnianie i szyfrowanie w celu zabezpieczania różnych aspektów klastra i jego aplikacji. Dla klienta dostępu/wykonywania operacji zarządzania w klastrze, w tym wdrażania, uaktualniania i usuwania aplikacji, usług i danych, które zawierają można użyć certyfikaty lub poświadczenia usługi Azure Active Directory. Korzystanie z usługi Azure Active Directory jest zdecydowanie zaleca się, ponieważ jest jedynym sposobem, aby uniemożliwić Udostępnianie certyfikatów na klientach.  Aby uzyskać więcej informacji na temat używania certyfikatów w usłudze Service Fabric, zobacz [scenariusze zabezpieczeń klastra usługi Service Fabric][service-fabric-cluster-security].

Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i udostępnia funkcje zabezpieczeń aplikacji. Możesz użyć [usługi Key Vault] [ key-vault-get-started] do zarządzania certyfikatami klastrów usługi Service Fabric na platformie Azure. 


### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagane)
Te certyfikaty (jedną podstawową i opcjonalnie pomocniczego) są wymagane do zabezpieczenia klastra i zapobiegania nieuprawnionemu dostępowi do niego. Oferuje ono zabezpieczenia klastra na dwa sposoby:

* **Uwierzytelnianie klastra:** uwierzytelnia komunikacji między węzłami dla Federacji klastra. Tylko węzły, które można potwierdzić swoją tożsamość za pomocą tego certyfikatu można dołączyć do klastra.
* **Uwierzytelnianie serwera:** uwierzytelnia punktów końcowych zarządzania klastrem na kliencie zarządzania, aby poinformować klienta zarządzania rozmawia rzeczywistych klastra, a nie "człowiek pośrodku". Ten certyfikat zapewnia również protokół SSL dla interfejsu API zarządzania protokołu HTTPS i narzędzia Service Fabric Explorer, za pośrednictwem protokołu HTTPS.

Do realizacji konkretnych celów, certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny. Te certyfikaty, zwykle mają rozszerzenia pfx lub PEM  
* Certyfikat musi zostać utworzona na potrzeby wymiany kluczy, co umożliwia eksport do pliku wymiany informacji osobistych (pfx).
* **Nazwa podmiotu certyfikatu musi odpowiadać domeny, która umożliwia dostęp do klastra usługi Service Fabric**. To dopasowanie jest wymagane, aby zapewnić protokół SSL dla punktu końcowego zarządzania HTTPS klastra i narzędzia Service Fabric Explorer. Nie można uzyskać certyfikatu SSL od urzędu certyfikacji (CA) dla *. cloudapp.azure.com domeny. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Konfigurowanie usługi Azure Active Directory do uwierzytelniania klienta (opcjonalne, ale zalecane)

Usługi Azure AD umożliwia zarządzanie dostępem użytkowników do aplikacji organizacji (znanych jako dzierżaw). Aplikacje są podzielone na tych z opartą na sieci web, interfejs użytkownika logowania i te w środowisku klienta natywnego. W tym artykule przyjęto założenie, że utworzono już dzierżawę. Jeśli nie masz, zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Klaster usługi Service Fabric udostępnia kilka punktów wejścia do jego funkcje zarządzania, w tym, oparta na sieci web [narzędzia Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W rezultacie utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra, jednej aplikacji sieci web i jednej aplikacji natywnej.

Więcej informacji o tym, jak je skonfigurować w dalszej części dokumentu.

### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów można zainstalować w klastrze ze względów bezpieczeństwa aplikacji. Przed utworzeniem klastra, należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają certyfikatów do zainstalowania w węzłach, takich jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.
* Szyfrowanie danych między węzłami podczas replikacji.

Pojęcie tworzenia zabezpieczonych klastrów jest taki sam, czy są one Linux lub Windows klastrów. 

### <a name="client-authentication-certificates-optional"></a>Certyfikaty uwierzytelniania klienta (opcjonalnie)
Administrator lub użytkownik operacji klienta programu można określić dowolną liczbę dodatkowych certyfikatów. Domyślnie certyfikat klastra uprawnieniami administratora klienta. Nie należy instalować tych dodatkowych certyfikatów klienta do klastra, po prostu musi zostać określony jako są dozwolone w konfiguracji klastra, muszą jednak można zainstalować na komputerach klienckich do łączenia z klastrem i wykonywać żadnych zarządzania operacje.


## <a name="prerequisites"></a>Wymagania wstępne 
Pojęcie tworzenia zabezpieczonych klastrów jest taki sam, czy są one Linux lub Windows klastrów. Ten przewodnik obejmuje korzystanie z programu Azure PowerShell lub wiersza polecenia platformy Azure w celu tworzenia nowych klastrów. Wymagania wstępne są:

-  [Program Azure PowerShell 4.1 i nowsze wersje] [ azure-powershell] lub [wiersza polecenia platformy Azure 2.0 i nowsze wersje][azure-CLI].
-  szczegółowe informacje można znaleźć w przypadku modułów usługi Service Fabric, w tym miejscu — [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) i [modułu interfejsu wiersza polecenia az SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Wdrażanie klastra przy użyciu modułu usługi Service Fabric RM

W tym dokumencie użyjemy programu powershell usługi Service Fabric RM i modułu interfejsu wiersza polecenia, aby wdrożyć klaster, programu PowerShell lub wiersza polecenia modułu umożliwia wielu scenariuszy. Pozwól nam przejść przez każdą z nich. Wybierz scenariusz, czy uważasz, że najlepiej odpowiada Twoim potrzebom. 

- Tworzenie nowego klastra 
    - przy użyciu systemu wygenerowany certyfikat z podpisem własnym
    - za pomocą certyfikatu już posiadasz

Możesz użyć domyślnego szablonu klastra lub szablonu, która już istnieje

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Tworzenie nowego klastra — przy użyciu systemu wygenerowany certyfikat z podpisem własnym

Aby utworzyć klaster, użyj następującego polecenia, jeśli chcesz, aby system, aby wygenerować certyfikat z podpisem własnym i umożliwia Zabezpieczanie klastra. To polecenie ustawia certyfikatu klastra podstawowego, który jest używany do zabezpieczania klastra i skonfigurować dostęp administratora do wykonywania operacji zarządzania przy użyciu tego certyfikatu.

### <a name="login-to-azure"></a>Zaloguj się do platformy Azure

```PowerShell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
```

```CLI
azure login
az account set --subscription $subscriptionId
```
#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Użyj domyślnego 5 węzeł 1 węzeł typu szablonu, który jest dostarczany w module do skonfigurowania klastra

Użyj następującego polecenia, aby utworzyć klaster szybko, przez określenie minimalnej liczby parametrów

Szablon, który jest używany jest dostępna w [przykłady szablonów usługi Azure Service Fabric: szablon windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablonu Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Poniższe polecenia w działa w przypadku tworzenia klastrów Windows i Linux, wystarczy określić odpowiednio systemu operacyjnego. Polecenia programu PowerShell/interfejsu wiersza polecenia również danych wyjściowych dla certyfikatu w określonym CertificateOutputFolder; jednak upewnij się, że folder certyfikat już utworzony. Wykonanie tego polecenia może w innych parametrów, takich jak jednostki SKU maszyny Wirtualnej również.

> [!NOTE]
> Poniżej Powershell polecenie działa tylko z programem Azure PowerShell Resource Manager w wersji > 6.1. Aby sprawdzić bieżącą wersję programu Azure PowerShell Resource Manager w wersji, uruchom następujące polecenie programu PowerShell "Get-Module AzureRM". Kliknij ten link, aby uaktualnić wersję programu PowerShell usługi Resource Manager platformy Azure. https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0
>
>
```PowerShell
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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

```CLI
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

#### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, która już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postępuj zgodnie z wskazówki i objaśnienia na [dostosowywania szablonu klastra] [ customize-your-cluster-template] poniższej sekcji.

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że wartości i sprawdź, czy wszystkie trzy certyfikatów związane z następujące nazwy parametrów w szablonie i plik parametrów ma wartość null w następujący sposób.

```Json
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


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

W tym miejscu jest równoważne polecenia interfejsu wiersza polecenia to samo. Zmień wartości w instrukcji declare odpowiednie wartości. Interfejs wiersza polecenia obsługuje wszystkie parametry, które obsługuje powyższego polecenia programu PowerShell.

```CLI
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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Tworzenie nowego klastra — przy użyciu certyfikatu zakupione od urzędu certyfikacji lub masz już

Aby utworzyć klaster, użyj następującego polecenia, jeśli masz certyfikat, którego chcesz użyć do zabezpieczenia klastra przy użyciu.

Jeśli jest to podpisany certyfikat urzędu certyfikacji i ostatecznie przy użyciu również do innych celów, następnie zalecane jest zapewniają oddzielnej grupie zasobów specjalnie dla własnego magazynu kluczy. Zaleca się umieścić usługi key vault w jego własnej grupie zasobów. Ta akcja umożliwia usunięcie grupy zasobów obliczeniowych i magazynu, w tym grupę zasobów, która zawiera Twój klaster usługi Service Fabric, bez utraty kluczy i wpisów tajnych. **Grupy zasobów, która zawiera Twój magazyn kluczy _musi znajdować się w tym samym regionie_ jako klaster, który jest używany.**


#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module"></a>Użyj domyślnego 5 węzeł 1 węzeł typu szablonu, który jest dostarczany w module
Szablon, który jest używany jest dostępna w [przykładów dla platformy Azure: szablon Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablonu Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

```CLI
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

#### <a name="use-the-custom-template-that-you-have"></a>Użyj szablonu niestandardowego, który masz 
Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postępuj zgodnie z wskazówki i objaśnienia na [dostosowywania szablonu klastra] [ customize-your-cluster-template] poniższej sekcji.

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że wartości i sprawdź, czy wszystkie trzy certyfikatów związane z następujące nazwy parametrów w szablonie i plik parametrów ma wartość null w następujący sposób.

```Json
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


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

W tym miejscu jest równoważne polecenia interfejsu wiersza polecenia to samo. Zmień wartości w instrukcji declare odpowiednie wartości.

```CLI
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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Za pomocą wskaźnika do hasła, które już zostały przekazane do magazynu kluczy

Aby użyć istniejącego magazynu kluczy, możesz _musisz ją włączyć dla wdrożenia_ umożliwia uzyskiwanie certyfikatów i zainstaluj go na węzłach klastra dostawcy zasobów obliczeniowych:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```
W tym miejscu jest równoważne polecenia interfejsu wiersza polecenia to samo. Zmień wartości w instrukcji declare odpowiednie wartości.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurowanie usługi Azure Active Directory do uwierzytelniania klientów

Usługi Azure AD umożliwia zarządzanie dostępem użytkowników do aplikacji organizacji (znanych jako dzierżaw). Aplikacje są podzielone na tych z opartą na sieci web, interfejs użytkownika logowania i te w środowisku klienta natywnego. W tym artykule przyjęto założenie, że utworzono już dzierżawę. Jeśli nie masz, zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Klaster usługi Service Fabric udostępnia kilka punktów wejścia do jego funkcje zarządzania, w tym, oparta na sieci web [narzędzia Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W rezultacie utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra, jednej aplikacji sieci web i jednej aplikacji natywnej.

Aby uprościć niektóre etapy konfigurowania usługi Azure AD z klastrem usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell.

> [!NOTE]
> Przed utworzeniem klastra, wykonaj następujące kroki. Ponieważ skrypty oczekuje nazwy klastra i punktów końcowych, wartości powinny być planowane i nie wartości, że masz już utworzoną.

1. [Pobierz skrypty] [ sf-aad-ps-script-download] do komputera.
2. Kliknij prawym przyciskiem myszy plik zip, wybierz **właściwości**, wybierz opcję **odblokowanie** pole wyboru, a następnie kliknij przycisk **Zastosuj**.
3. Wyodrębnij plik ZIP.
4. Uruchom `SetupApplications.ps1`i podaj identyfikator dzierżawy, ClusterName i WebApplicationReplyUrl jako parametry. Na przykład:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

Wykonując polecenie programu PowerShell można znaleźć identyfikator TenantId `Get-AzureSubscription`. Wykonywanie to polecenie wyświetla identyfikator TenantId dla każdej subskrypcji.

ClusterName służy jako prefiks aplikacji usługi Azure AD, które są tworzone przez skrypt. Nie musi dokładnie odpowiadać rzeczywista nazwa klastra. Jest ona przeznaczona tylko po to, aby ułatwić mapowania klastra usługi Service Fabric, który jest używany z artefaktów w usłudze Azure AD.

WebApplicationReplyUrl jest domyślny punkt końcowy zwracające usługi Azure AD dla użytkowników po ich zakończeniu logowania. Ustaw ten punkt końcowy jako punkt końcowy narzędzia Service Fabric Explorer dla klastra, która domyślnie jest:

https://&lt;cluster_domain&gt;:19080/Explorer

Monit logować się do konta z uprawnieniami administratora dla dzierżawy usługi Azure AD. Po zalogowaniu, skrypt tworzy sieci web i aplikacji natywnych, aby reprezentować klaster usługi Service Fabric. Jeśli spojrzysz na aplikacje dzierżawcy w [witryny Azure portal][azure-portal], powinny zostać wyświetlone dwa nowe wpisy:

   * *ClusterName*\_klastra
   * *ClusterName*\_klienta

Skrypt wyświetla plik JSON wymagany przez szablon usługi Azure Resource Manager podczas tworzenia klastra w następnej sekcji, więc to dobry pomysł, aby nie zamykaj okna programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Tworzenie szablonu usługi resource manager klastra usługi Service Fabric
Ta sekcja dotyczy dla użytkowników, którzy chcą z niestandardowymi Tworzenie szablonu usługi resource manager klastra usługi Service Fabric. Po utworzeniu szablonu, można nadal Przejdź wstecz i użyj modułów programu PowerShell lub interfejsu wiersza polecenia, aby wdrożyć go. 

Menedżer zasobów przykładowe szablony są dostępne w [przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony może służyć jako punkt wyjścia do szablonu klastra.

### <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
W tym przewodniku używane [bezpiecznego klastra z 5 węzłami] [ service-fabric-secure-cluster-5-node-1-nodetype] przykładowy szablon i parametry szablonu. Pobierz `azuredeploy.json` i `azuredeploy.parameters.json` do komputera, a następnie otwórz oba pliki w swoim ulubionym edytorze tekstów.

### <a name="add-certificates"></a>Dodaj certyfikaty
Certyfikaty można dodawać do szablonu usługi resource manager klastra, odwołując się do magazynu kluczy, która zawiera klucze certyfikatu. Dodaj te parametry magazynu kluczy i wartości w pliku parametrów szablonu usługi Resource Manager (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Dodaj wszystkie certyfikaty do osProfile zestaw skali maszyny wirtualnej
Każdy certyfikat, który jest zainstalowany w klastrze muszą być skonfigurowane w sekcji osProfile zasobu zestawu skalowania (Microsoft.Compute/virtualMachineScaleSets). Ta akcja powoduje, że dostawca zasobów, aby zainstalować certyfikat na maszynach wirtualnych. Ta instalacja obejmuje zarówno certyfikat klastra, jak i wszelkich certyfikatów zabezpieczeń aplikacji, które będą używane dla aplikacji:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurowanie certyfikatu klastra usługi Service Fabric
Certyfikat uwierzytelniania w klastrze muszą być skonfigurowane w obu usługi Service Fabric zasobu klastra (Microsoft.ServiceFabric/clusters) i rozszerzenia usługi Service Fabric dla skalowania maszyn wirtualnych zestawy w zasobu zestawu skalowania maszyny wirtualnej. To rozwiązanie umożliwia dostawcy zasobów usługi Service Fabric skonfigurować go do użycia do uwierzytelniania w klastrze i uwierzytelnianie serwera dla punktów końcowych zarządzania.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Dodawanie zasobu zestawu skalowania maszyn wirtualnych informacje o certyfikacie:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Dodaj informacje o certyfikacie do zasobu klastra usługi Service Fabric:
```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Dodaj konfigurację usługi Azure AD, aby używać usługi Azure AD dla dostępu klientów

Konfiguracji usługi Azure AD jest dodawanie do szablonu usługi Resource Manager klastra, odwołując się do magazynu kluczy, która zawiera klucze certyfikatu. Dodaj te parametry usługi Azure AD i wartości w pliku parametrów szablonu usługi Resource Manager (azuredeploy.parameters.json).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
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

### <a name="populate-the-parameter-file-with-the-values"></a>Wypełnij plik parametrów z wartościami
Na koniec użyj wartości danych wyjściowych z poleceń programu PowerShell usługi Azure AD i usługi key vault, aby wypełnić plik parametrów.

Jeśli planujesz użyć modułów programu PowerShell usługi Azure Service fabric Menedżera zasobów, nie trzeba wypełnić informacji o certyfikacie klastra. Jeśli chcesz, aby system wygenerował samodzielnie podpisanego certyfikatu do zabezpieczania klastra, wystarczy przechowywać je jako wartości null. 

> [!NOTE]
> Dla modułów RM na odebranie i wypełnić te wartości parametrów empty nazwy parametrów znacznie być zgodne z nazwami poniżej

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Jeśli używasz certyfikatów aplikacji lub korzystasz z istniejącego klastra, które zostały przekazane do magazynu kluczy, należy uzyskać te informacje i wypełnić je.

Moduły Menedżera zasobów nie mają możliwość generowania konfiguracji usługi Azure AD, dlatego jeśli planujesz używać usługi Azure AD dla dostępu klientów, należy go wypełnić.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>Test szablonu  
Użyj następującego polecenia programu PowerShell na potrzeby testów w pliku parametrów szablonu usługi Resource Manager:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku napotkania problemów i Pobierz one niezrozumiałe komunikaty, następnie za pomocą "-Debug" jako opcję.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Na poniższym diagramie przedstawiono, w którym magazyn kluczy i konfiguracji usługi Azure AD mieści się w szablonie usługi Resource Manager.

![Mapa zależności usługi Resource Manager][cluster-security-arm-dependency-map]


## <a name="encrypting-the-disks-attached-to-your-windows-cluster-nodevirtual-machine-instances"></a>Szyfrowanie dysków dołączonych do aplikacji dla systemu windows wystąpienia maszyny/wirtualnego węzła klastra

Szyfrowanie dysków (dysków systemu operacyjnego i innych dysków zarządzanych) dołączonych do węzłów, będziemy korzystać z usługi Azure Disk Encryption. Usługa Azure Disk Encryption to nowa funkcja, która ułatwia [szyfrowanie dysków maszyn wirtualnych Windows](service-fabric-enable-azure-disk-encryption-windows.md). Usługa Azure Disk Encryption korzysta z branżowymi [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcja systemu Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego. To rozwiązanie jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage. 

## <a name="encrypting-the-disks-attached-to-your-linux-cluster-nodevirtual-machine-instances"></a>Możliwości szyfrowania dysków dołączonych do wystąpień systemu Linux klastra węzeł/wirtualnego maszyny

Szyfrowanie dysków (dysk danych i innych dysków zarządzanych) dołączonych do węzłów, będziemy korzystać z usługi Azure Disk Encryption. Usługa Azure Disk Encryption to nowa funkcja, która ułatwia [szyfrowanie dysków maszyny wirtualnej systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md). Usługa Azure Disk Encryption korzysta z branżowymi [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage. 


## <a name="create-the-cluster-using-azure-resource-template"></a>Tworzenie klastra przy użyciu szablonu zasobów platformy Azure 

Teraz można wdrożyć klaster przy użyciu kroków opisanych w dokumencie lub jeśli masz wartości w pliku parametrów wypełnione, następnie teraz można przystąpić do utworzenia klastra przy użyciu [wdrażania szablonu usługi Azure resource] [ resource-group-template-deploy] bezpośrednio.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku napotkania problemów i Pobierz one niezrozumiałe komunikaty, następnie za pomocą "-Debug" jako opcję.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji ma reprezentować klaster przypisać użytkowników do ról obsługiwanych przez usługę Service Fabric: tylko do odczytu i administratora. Role można przypisać za pomocą [witryny Azure portal][azure-portal].

1. W witrynie Azure portal wybierz dzierżawy w prawym górnym rogu.

    ![Wybierz przycisk dzierżawy][select-tenant-button]
2. Wybierz **usługi Azure Active Directory** na karcie po lewej stronie, a następnie wybierz opcję "aplikacje dla przedsiębiorstw".
3. Wybierz pozycję "Wszystkie aplikacje", a następnie znajdź i wybierz aplikację sieci web, która ma nazwę takich jak `myTestCluster_Cluster`.
4. Kliknij przycisk **użytkowników i grup** kartę.

    ![Karcie Użytkownicy i grupy][users-and-groups-tab]
5. Kliknij przycisk **Dodaj użytkownika** znajdujący się na nowej stronie, wybierz użytkownika i rolę do przypisania, a następnie kliknij przycisk **wybierz** znajdujący się u dołu strony.

    ![Przypisywanie użytkowników do ról strony][assign-users-to-roles-page]
6. Kliknij przycisk **przypisać** znajdujący się u dołu strony.

    ![Dodaj Potwierdzenie przypisania][assign-users-to-roles-confirm]

> [!NOTE]
> Aby uzyskać więcej informacji o rolach w usłudze Service Fabric, zobacz [kontroli dostępu opartej na rolach dla klientów usługi Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Rozwiązywanie problemów z pomocy w konfigurowaniu usługi Azure Active Directory
Konfigurowanie usługi Azure AD i korzystania z niego może stanowić wyzwanie, poniżej przedstawiono niektóre wskaźników, co można zrobić, aby debugować ten problem.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Narzędzie Service Fabric Explorer wyświetla monit o wybranie certyfikatu
#### <a name="problem"></a>Problem
Po zalogowaniu pomyślnie z usługą Azure AD w narzędziu Service Fabric Explorer, przeglądarka powraca do strony głównej, ale komunikat wyświetla monit o wybranie certyfikatu.

![Okno dialogowe certyfikat SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Przyczyna
Użytkownik nie ma przypisaną rolę w aplikacji do klastra usługi Azure AD. W związku z tym uwierzytelnianie usługi Azure AD nie powiedzie się w klastrze usługi Service Fabric. Narzędzie Service Fabric Explorer powraca do uwierzytelniania certyfikatu.

#### <a name="solution"></a>Rozwiązanie
Wykonaj instrukcje dotyczące konfigurowania usługi Azure AD i przypisz role użytkownika. Ponadto zaleca się włączenie funkcji "Wymagany dostęp do aplikacji, przypisywanie użytkownika" jako `SetupApplications.ps1` jest.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Połączenie przy użyciu programu PowerShell zakończy się niepowodzeniem z powodu błędu: "określone poświadczenia są nieprawidłowe"
#### <a name="problem"></a>Problem
Jeśli używasz programu PowerShell do łączenia z klastrem przy użyciu trybu zabezpieczeń "Usługi AzureActiveDirectory", po zalogowaniu pomyślnie z usługą Azure AD, połączenie kończy się niepowodzeniem z powodu błędu: "określone poświadczenia są nieprawidłowe."

#### <a name="solution"></a>Rozwiązanie
To rozwiązanie jest taka sama jak poprzedni.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Narzędzie Service Fabric Explorer zwraca błąd, podczas logowania: "AADSTS50011"
#### <a name="problem"></a>Problem
Przy próbie Zaloguj się do usługi Azure AD w narzędziu Service Fabric Explorer strony zwraca błąd: "AADSTS50011: adres zwrotny &lt;adresu url&gt; jest niezgodna z adresy zwrotne skonfigurowane dla aplikacji: &lt;guid&gt;."

![Adres zwrotny SFX nie jest zgodny.][sfx-reply-address-not-match]

#### <a name="reason"></a>Przyczyna
Aplikacji klastra (sieć web), który reprezentuje narzędzia Service Fabric Explorer podejmie próbę uwierzytelniania w usłudze Azure AD i jako część żądania zapewnia zwrotny adres URL przekierowania. Adres URL nie znajduje się w aplikacji usługi Azure AD, ale **adres URL odpowiedzi** listy.

#### <a name="solution"></a>Rozwiązanie
Wybierz pozycję "Rejestracje aplikacji" na stronie usługi AAD, wybierz swoją aplikację w klastrze, a następnie wybierz **adresy URL odpowiedzi** przycisku. Na stronie "Adresów URL odpowiedzi" Dodaj adres URL z Eksploratora usługi Service Fabric do listy lub Zastąp jeden z elementów na liście. Po zakończeniu zapisz zmiany.

![Adres url odpowiedzi aplikacji sieci Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Połącz klaster przy użyciu uwierzytelniania usługi Azure AD za pomocą programu PowerShell
Aby połączyć z klastra usługi Service Fabric, skorzystaj z poniższego przykładu polecenie programu PowerShell:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Aby dowiedzieć się więcej na temat polecenia cmdlet Connect-ServiceFabricCluster, zobacz [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Czy mogę ponownie użyć tej samej dzierżawie usługi Azure AD w wielu klastrach?
Tak. Należy jednak pamiętać dodać adres URL z Eksploratora usługi Service Fabric do aplikacji klastra (sieć web). W przeciwnym razie Eksploratora usługi Service Fabric nie działa.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Dlaczego nadal należy certyfikat serwera po włączeniu usługi Azure AD?
FabricClient i FabricGateway przeprowadza uwierzytelnianie wzajemne. Podczas uwierzytelniania usługi Azure AD integracji z usługą Azure AD zapewnia usługi tożsamości klienta, na serwerze, a certyfikat serwera jest używana do zweryfikowania tożsamości serwera. Aby uzyskać więcej informacji o certyfikatach usługi Service Fabric, zobacz [certyfikaty X.509 i Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz zabezpieczonego klastra przy użyciu usługi Azure Active Directory dostarczanie zarządzania uwierzytelniania. Następnie [połączenia z klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak [Zarządzanie wpisami tajnymi aplikacji](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

