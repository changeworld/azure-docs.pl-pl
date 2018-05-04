---
title: Tworzenie klastra usługi sieć szkieletowa usług Azure na podstawie szablonu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skonfigurować bezpieczny klastra sieci szkieletowej usług na platformie Azure przy użyciu usługi Azure Resource Manager, magazyn kluczy Azure i usługi Azure Active Directory (Azure AD) do uwierzytelniania klientów.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: aljo
ms.openlocfilehash: e3e9e0c13368dbf7dd32c8483f8e6783afc1bdbb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Tworzenie klastra sieci szkieletowej usług za pomocą usługi Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ten przewodnik krok po kroku przeprowadzi Cię przez proces konfigurowania bezpiecznej klastra usługi sieć szkieletowa usług Azure na platformie Azure przy użyciu usługi Azure Resource Manager. Potwierdzam, że artykuł jest długa. Niemniej jednak jeśli nie masz już dokładnie zapoznać się z zawartości, należy dokładnie wykonaj wszystkie kroki.

Przewodnik obejmuje następujące procedury:

* Kluczowe założenia, które należy znać wyłączone przed wdrożeniem klastra sieci szkieletowej usług.
* Tworzenie klastra na platformie Azure przy użyciu modułów Menedżera zasobów sieci szkieletowej usług.
* Konfigurowanie usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników wykonywanie operacji zarządzania w klastrze.
* Do tworzenia szablonu niestandardowego usługi Azure Resource Manager dla klastra i ich wdrażania.

## <a name="key-concepts-to-be-aware-of"></a>Kluczowe założenia wiedzieć
Na platformie Azure Service fabric ma który x509 użycia certyfikatu do zabezpieczenia klastra i jego punktów końcowych. W usłudze Service Fabric używa się certyfikatów, aby zapewniać uwierzytelnianie i szyfrowanie w celu zabezpieczania różnych aspektów klastra i jego aplikacji. Dla klienta dostępu/wykonywanie operacji zarządzania w klastrze, w tym wdrażanie, uaktualnianie i usuwanie aplikacji, usług i danych, które zawierają można używać certyfikatów lub poświadczeń usługi Azure Active Directory. Korzystanie z usługi Azure Active Directory jest zdecydowanie zaleca, ponieważ jest jedynym sposobem, aby uniemożliwić Udostępnianie certyfikatów na klientach.  Aby uzyskać więcej informacji dotyczących używania certyfikatów w sieci szkieletowej usług, zobacz [scenariusze zabezpieczeń klastra sieci szkieletowej usług][service-fabric-cluster-security].

Sieć szkieletowa usług używa certyfikatów X.509 do zabezpieczania klastra oraz udostępnia funkcje zabezpieczeń aplikacji. Możesz użyć [Key Vault] [ key-vault-get-started] Aby zarządzać certyfikatami dla klastrów sieci szkieletowej usług platformy Azure. 


### <a name="cluster-and-server-certificate-required"></a>Klaster a serwera certyfikatów (wymagane)
Te certyfikaty (podstawowego i opcjonalnie dodatkowej) są wymagane do bezpiecznego klastra i uniemożliwić nieautoryzowany dostęp do niego. Udostępnia ona zabezpieczeń klastra na dwa sposoby:

* **Uwierzytelnianie klastra:** uwierzytelnia komunikacji między węzłami w Federacji klastra. Tylko węzły, które można potwierdzić swoją tożsamość, z tym certyfikatem można dołączyć do klastra.
* **Uwierzytelnianie serwera:** uwierzytelnia punktów końcowych klastra zarządzania jak klient zarządzania, dzięki czemu klienta zarządzania zna rozmawia rzeczywistych klastra i nie "man w środku". Ten certyfikat zapewnia również SSL interfejsu API zarządzania HTTPS oraz Service Fabric Explorer, za pośrednictwem protokołu HTTPS.

Do obsługi tych celów, certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny. Te certyfikaty zwykle mają rozszerzenia pfx lub PEM  
* Certyfikat musi zostać utworzony dla wymiany kluczy, które można wyeksportować do pliku wymiany informacji osobistych (pfx).
* **Nazwa podmiotu certyfikatu musi odpowiadać domeny, który umożliwia dostęp do klastra usługi sieć szkieletowa**. To dopasowanie jest wymagane jest podanie protokołu SSL dla punktu końcowego zarządzania HTTPS i Service Fabric Explorer klastra. Nie można uzyskać certyfikat od urzędu certyfikacji (CA) dla *. cloudapp.azure.com domeny. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Konfigurowanie usługi Azure Active Directory do uwierzytelniania klienta (opcjonalne, ale zalecane)

Usługi Azure AD umożliwia organizacjom (nazywane dzierżawcy), aby zarządzać dostępem użytkowników do aplikacji. Aplikacje są podzielone na tych z opartą na sieci web interfejsu użytkownika logowania i te przy użyciu środowiska macierzystego klienta. W tym artykule przyjęto założenie, że utworzono już dzierżawcy. Jeśli nie masz, Rozpocznij od przeczytania [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Klaster sieci szkieletowej usług oferuje kilka punktów wejścia do jego funkcjonalność zarządzania, w tym oparta na sieci web [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W związku z tym tworzone są dwie aplikacje usługi Azure AD do kontrolowania dostępu do klastra, jednej aplikacji sieci web i jednej aplikacji natywnej.

Więcej informacji na temat sposobu konfigurowania go w dalszej części dokumentu.

### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów można zainstalować w klastrze dla aplikacji ze względów bezpieczeństwa. Przed utworzeniem klastra, należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają certyfikatów do zainstalowania w węzłach, takich jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.
* Szyfrowanie danych w węzłach podczas replikacji.

Pojęcie tworzenia bezpiecznych klastrów jest taki sam, czy są one Linux lub klastry z systemem Windows. 

### <a name="client-authentication-certificates-optional"></a>Certyfikaty uwierzytelniania klienta (opcjonalnie)
Dla administratora lub użytkownika operacje klienta można określić dowolną liczbę dodatkowych certyfikatów. Domyślnie certyfikat klastra ma uprawnienia administratora klienta. Nie należy instalować tych certyfikatów dodatkowe klienta do klastra, po prostu musi być określone jako dozwolonych w konfiguracji klastra, jednak muszą być zainstalowane na komputerach klienckich, aby połączyć się z klastrem i wykonywanie działań z zakresu zarządzania operacje.


## <a name="prerequisites"></a>Wymagania wstępne 
Pojęcie tworzenia bezpiecznych klastrów jest taki sam, czy są one Linux lub klastry z systemem Windows. W tym przewodniku opisano połączone użycie programu azure powershell lub azure CLI do tworzenia nowych klastrów. Wymagania wstępne są albo 

-  [Program Azure PowerShell 4.1 i powyżej] [ azure-powershell] lub [Azure CLI 2.0 lub nowszej][azure-CLI].
-  szczegółowe informacje można znaleźć na fabic modułów usługi tutaj — [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) i [az rz modułu interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Umożliwia wdrażanie klastra usługi sieć szkieletowa RM modułu

W tym dokumencie możemy użyć programu powershell Menedżera zasobów sieci szkieletowej usług i moduł interfejsu wiersza polecenia do wdrożenia klastra, programu powershell lub interfejsu wiersza polecenia polecenie modułu umożliwia wielu scenariuszy. Daj nam przejść przez każdego z nich. Pobranie scenariusza można uznać najlepsze spełni wymagania organizacji. 

- Tworzenie nowego klastra — przy użyciu systemu wygenerowanego certyfikatu z podpisem własnym
    - Użyć szablonu domyślnego klastra
    - Użyj szablonu, który już istnieje
- Tworzenie nowego klastra — używa certyfikatu, masz już
    - Użyć szablonu domyślnego klastra
    - Użyj szablonu, który już istnieje

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Utwórz nowy klaster — przy użyciu systemu wygenerowanego certyfikatu z podpisem własnym

Użyj następującego polecenia do tworzenia klastra, określ, czy masz system generowania certyfikatu z podpisem własnym i użyć go do zabezpieczania klastra. To polecenie ustawia klastra głównego certyfikatu, który jest używany do zabezpieczenia klastra i skonfigurować dostęp administratora do wykonywania operacji zarządzania przy użyciu tego certyfikatu.

### <a name="login-in-to-azure"></a>Zaloguj się w na platformie Azure.

```Powershell

Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Użyj domyślnego 5 szablonu nodetype 1 węzła, który jest dostarczany w module do skonfigurowania klastra

Użyj następującego polecenia, aby utworzyć klaster szybkie, określając parametry minimalnego

Szablon używany jest dostępna na [przykłady szablonu sieci szkieletowej usług azure: szablonu systemu windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [Ubuntu szablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Polecenia poniżej działa w przypadku tworzenia klastrów systemu Windows i Linux, wystarczy określić odpowiednio systemu operacyjnego. Programu PowerShell / polecenia interfejsu wiersza polecenia również certyfikat w określonym CertificateOutputFolder jednak upewnij się, że dane wyjściowe certyfikatu folder już utworzony. Polecenie przyjmuje inne parametry, takie jak SKU maszyna wirtualna również.

```Powershell

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

#### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, który już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest uruchomienie z jednego z szablonów, które są dostępne na [przykłady szablonu sieci szkieletowej usług azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Wykonaj wskazówki i objaśnienia do [Dostosuj szablon klastra] [ customize-your-cluster-template] poniższej sekcji.

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że podwójne zaznaczenie, wszystkie trzy certyfikat powiązany to następujące nazwy parametrów w szablonie i pliku parametrów i wartości mają wartość null w następujący sposób.

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

Oto analogiczne polecenie interfejsu wiersza polecenia, aby zrobić to samo. Zmień wartości w instrukcji declare odpowiednie wartości. Interfejs wiersza polecenia obsługuje wszystkie parametry, które obsługuje powyższe polecenie programu PowerShell.

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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Utwórz nowy klaster — przy użyciu certyfikatu zakupiono od urzędu certyfikacji lub już istnieje.

Jeśli masz certyfikat, który ma być używany do zabezpieczania klastra za pomocą, użyj następującego polecenia do utworzenia klastra.

Jeśli jest to podpisem certyfikatu urzędu certyfikacji, który spowoduje utworzenie za pomocą do innych celów, jak również, następnie zaleca Podaj grupę zasobów różne specjalnie dla magazynu kluczy. Firma Microsoft zaleca umieścić magazynu kluczy w jego własnej grupy zasobów. Ta akcja umożliwia usunięcie grupy zasobów obliczeniowych i przestrzeni dyskowej, wraz z grupy zasobów zawierającej klaster sieci szkieletowej usług, bez utraty z kluczy i kluczy tajnych. **Grupy zasobów, która zawiera magazynu kluczy _musi znajdować się w tym samym regionie_ co klaster, który go używa.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Użyj domyślnego 5 szablonu nodetype 1 węzła, który jest dostarczany w module
Szablon, który jest używany jest dostępna na [przykładów dla platformy azure: szablon windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [Ubuntu szablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

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

#### <a name="use-the-custom-template-that-you-have"></a>Użyj szablonu niestandardowego, czy masz 
Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest uruchomienie z jednego z szablonów, które są dostępne na [przykłady szablonu sieci szkieletowej usług azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Wykonaj wskazówki i objaśnienia do [Dostosuj szablon klastra] [ customize-your-cluster-template] poniższej sekcji.

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że podwójne zaznaczenie, wszystkie trzy certyfikat powiązany to następujące nazwy parametrów w szablonie i pliku parametrów i wartości mają wartość null w następujący sposób.

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

Oto analogiczne polecenie interfejsu wiersza polecenia, aby zrobić to samo. Zmień wartości w instrukcji declare odpowiednie wartości.

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

Aby użyć istniejącego magazynu kluczy, możesz _należy włączyć dla wdrożenia_ umożliwia dostawcy zasobów obliczeniowych uzyskanie certyfikatów i zainstalować go na węzłach klastra:

```PowerShell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```
Oto analogiczne polecenie interfejsu wiersza polecenia, aby zrobić to samo. Zmień wartości w instrukcji declare odpowiednie wartości.

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

Usługi Azure AD umożliwia organizacjom (nazywane dzierżawcy), aby zarządzać dostępem użytkowników do aplikacji. Aplikacje są podzielone na tych z opartą na sieci web interfejsu użytkownika logowania i te przy użyciu środowiska macierzystego klienta. W tym artykule przyjęto założenie, że utworzono już dzierżawcy. Jeśli nie masz, Rozpocznij od przeczytania [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Klaster sieci szkieletowej usług oferuje kilka punktów wejścia do jego funkcjonalność zarządzania, w tym oparta na sieci web [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W związku z tym tworzone są dwie aplikacje usługi Azure AD do kontrolowania dostępu do klastra, jednej aplikacji sieci web i jednej aplikacji natywnej.

Aby uprościć niektóre etapy konfigurowania usługi Azure AD z klastrem usługi sieć szkieletowa, został utworzony zestaw skryptów programu Windows PowerShell.

> [!NOTE]
> Przed utworzeniem klastra, wykonaj następujące kroki. Ponieważ skrypty oczekuje nazwy klastra i punktów końcowych, wartości powinny zostać zaplanowane, a nie wartości, które zostało już utworzone.

1. [Pobierz skrypty] [ sf-aad-ps-script-download] do komputera.
2. Kliknij prawym przyciskiem myszy plik zip, wybierz **właściwości**, wybierz pozycję **Odblokuj** pole wyboru, a następnie kliknij przycisk **Zastosuj**.
3. Wyodrębnij plik ZIP.
4. Uruchom `SetupApplications.ps1`i podaj TenantId ClusterName i WebApplicationReplyUrl jako parametry. Na przykład:

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

Wykonując polecenie programu PowerShell można znaleźć Twojego identyfikatora dzierżawcy `Get-AzureSubscription`. Wykonywanie to polecenie wyświetla TenantId dla każdej subskrypcji.

ClusterName jest używana do prefiksu aplikacji usługi Azure AD, które są tworzone przez skrypt. Nie musi dokładnie odpowiadać nazwie rzeczywiste klastra. Ma ona tylko ułatwiają mapy usługi Azure AD artefakty do klastra usługi sieć szkieletowa, który jest używany z.

WebApplicationReplyUrl jest domyślny punkt końcowy zwracające usługi Azure AD dla użytkowników po kończyły się zalogować. Ustaw ten punkt końcowy jako punkt końcowy Service Fabric Explorer dla klastra, która domyślnie jest:

https://&lt;cluster_domain&gt;:19080/Explorer

Zostanie wyświetlony monit Zaloguj się do konta z uprawnieniami administratora dla dzierżawy usługi Azure AD. Po zalogowaniu, skrypt tworzy sieć web i natywnych aplikacji do reprezentowania klastra sieci szkieletowej usług. Jeśli przyjrzymy się aplikacji dzierżawcy w [portalu Azure][azure-portal], powinny pojawić się dwa nowe wpisy:

   * *ClusterName*\_klastra
   * *ClusterName*\_klienta

Skrypt drukuje JSON wymagane przez szablon Menedżera zasobów Azure, podczas tworzenia klastra w następnej sekcji, dlatego warto nie zamykaj okna programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Utwórz szablon Menedżera zasobów klastra sieci szkieletowej usług
W tej sekcji jest dla użytkowników, którzy chcą niestandardowy tworzenia szablonu usługi Resource Manager klastra sieci szkieletowej usług. Po utworzeniu szablonu, nadal można wrócić do poprzedniej strony i użyj programu powershell lub moduły interfejsu wiersza polecenia, aby wdrożyć ją. 

Menedżer zasobów przykładowe szablony są dostępne w [przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony może służyć jako punkt początkowy dla szablonu klastra.

### <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
W tym przewodniku używa [5 węzłów klastra bezpiecznego] [ service-fabric-secure-cluster-5-node-1-nodetype] przykładowy szablon i parametrów szablonu. Pobierz `azuredeploy.json` i `azuredeploy.parameters.json` do komputera, a następnie otwórz oba pliki w edytorze tekstu.

### <a name="add-certificates"></a>Dodaj certyfikaty
Dodaj certyfikaty do szablonu usługi Resource Manager klastra odbywa się za pomocą odwołań do magazynu kluczy, która zawiera klucze certyfikatu. Dodaj te parametry magazyn kluczy i wartości w pliku parametrów szablonu usługi Resource Manager (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Dodaj wszystkie certyfikaty do osProfile zestaw skali maszyny wirtualnej
Każdy certyfikat, który jest zainstalowany w klastrze muszą być skonfigurowane w sekcji osProfile zasób zestawu skali (Microsoft.Compute/virtualMachineScaleSets). Ta akcja powoduje, że dostawca zasobów, aby zainstalować certyfikat na maszynach wirtualnych. Ta instalacja obejmuje zarówno certyfikat klastra, jak i wszelkich certyfikatów zabezpieczeń aplikacji, które będą używane dla aplikacji:

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

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurowanie certyfikatu klastra sieci szkieletowej usług
Certyfikat uwierzytelniania klastra muszą być skonfigurowane w obu sieci szkieletowej usług zasobu klastra (Microsoft.ServiceFabric/clusters) i rozszerzenia skalowania maszyny wirtualnej dla sieci szkieletowej usług ustawia w zasobie zestaw skali maszyny wirtualnej. To rozmieszczenie umożliwia jest skonfigurowana do użycia uwierzytelniania klastra i uwierzytelniania serwera dla punktów końcowych zarządzania dostawcy zasobów sieci szkieletowej usług.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Dodaj informacje o certyfikacie skali maszyny wirtualnej zestawu zasobów:
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Dodawanie informacji o certyfikacie do zasobu klastra usługi sieć szkieletowa usług:
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

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Dodaj konfigurację usługi Azure AD dla dostępu klientów przy użyciu usługi Azure AD

Konfiguracji usługi Azure AD s należy dodać do szablonu usługi Resource Manager klastra za pomocą odwołań do magazynu kluczy, która zawiera klucze certyfikatu. Dodaj te parametry usługi Azure AD i wartości w pliku parametrów szablonu usługi Resource Manager (azuredeploy.parameters.json).

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

### <a name="populate-the-parameter-file-with-the-values"></a>Wypełnij pliku parametrów z wartościami.
Na koniec użyj wartości danych wyjściowych z magazynu kluczy i poleceń programu PowerShell usługi Azure AD do wypełnienia pliku parametrów:

Jeśli planujesz używać sieci szkieletowej usług Azure, moduły programu RM PowerShell, możesz nie ma potrzeby wypełnić informacji o certyfikacie klastra, jeśli ma system do generowania samoobsługowego podpisany certyfikat zabezpieczeń klastra możesz po prostu zachowywać je jako wartość null. 

> [!NOTE]
> Dla modułów RM na odebranie i wypełnić wartości tych parametrów pusta nazwy parametrów znacznie pasują poniższe nazwy
>

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

Jeśli używasz aplikacji certyfikaty lub korzystają z istniejącego klastra, które zostały przekazane do magazynu kluczy, należy pobrać tych informacji i wypełnić je 

Moduły protokołu RM nie mają możliwość wygenerowania konfiguracji usługi Azure AD. Dlatego jeśli planujesz używać usługi Azure AD dla dostępu klientów, należy umieścić w nim.

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

### <a name="test-your-template"></a>Testowanie szablonu  
Do przetestowania pliku parametrów szablonu usługi Resource Manager, należy użyć następującego polecenia programu PowerShell:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku napotkania problemów i pobieranie wiadomości one niezrozumiałe, następnie użyj "-Debug" jako opcja.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Na poniższym diagramie przedstawiono, w którym magazyn kluczy i konfiguracji usługi Azure AD mieści się w szablonie usługi Resource Manager.

![Menedżer zasobów zależności mapy][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Tworzenie klastra przy użyciu szablonu zasobów platformy Azure 

Teraz można wdrożyć klaster przy użyciu kroków opisanych wcześniej w dokumencie lub jeśli wartości w pliku parametrów wypełnione, następnie teraz można przystąpić do tworzenia klastra przy użyciu [wdrażania szablonu zasobów platformy Azure] [ resource-group-template-deploy] bezpośrednio.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku napotkania problemów i pobieranie wiadomości one niezrozumiałe, następnie użyj "-Debug" jako opcja.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji do reprezentowania klastra przypisać użytkowników do ról obsługiwane przez usługi Service Fabric: tylko do odczytu i administratora. Role można przypisać za pomocą [portalu Azure][azure-portal].

1. W portalu Azure wybierz dzierżawy w prawym górnym rogu.

    ![Wybierz przycisk dzierżawy][select-tenant-button]
2. Wybierz **usługi Azure Active Directory** na karcie po lewej stronie, a następnie wybierz opcję "aplikacje przedsiębiorstwa".
3. Wybierz opcję "Wszystkie aplikacje", a następnie znajdź i wybierz aplikację sieci web, która zawiera nazwę jak `myTestCluster_Cluster`.
4. Kliknij przycisk **użytkowników i grup** kartę.

    ![Karta użytkowników i grup][users-and-groups-tab]
5. Kliknij przycisk **Dodaj użytkownika** znajdującego się na nowej stronie, wybierz użytkownika i roli, przypisywanie, a następnie kliknij przycisk **wybierz** u dołu strony.

    ![Przypisywanie użytkowników do ról strony][assign-users-to-roles-page]
6. Kliknij przycisk **przypisać** u dołu strony.

    ![Dodaj Potwierdzenie przypisania][assign-users-to-roles-confirm]

> [!NOTE]
> Aby uzyskać więcej informacji na temat ról w sieci szkieletowej usług, zobacz [kontroli dostępu opartej na rolach dla klientów usługi sieć szkieletowa](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Rozwiązywanie problemów z pomocy w procesie konfigurowania usługi Azure Active Directory
Konfigurowanie usługi Azure AD i przy jego użyciu, może być wyzwaniem, dlatego poniżej przedstawiono wskazówki na co można zrobić, aby debugować ten problem.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer wyświetla monit o wybranie certyfikatu
#### <a name="problem"></a>Problem
Po zalogowaniu pomyślnie do usługi Azure AD w narzędziu Service Fabric Explorer, zwraca przeglądarkę na stronę główną, ale monit o wybranie certyfikatu.

![Okno dialogowe SFX certyfikatu][sfx-select-certificate-dialog]

#### <a name="reason"></a>Przyczyna
Użytkownik nie jest przypisana rola w aplikacji klastra usługi Azure AD. W związku z tym niepowodzenia uwierzytelniania usługi Azure AD w klastrze usługi sieć szkieletowa usług. Service Fabric Explorer powraca do uwierzytelniania certyfikatów.

#### <a name="solution"></a>Rozwiązanie
Instrukcje dotyczące konfigurowania usługi Azure AD i przypisz role użytkownika. Ponadto zalecamy włączenie "Przypisanie użytkownika wymagane na dostęp do aplikacji," jako `SetupApplications.ps1` jest.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Połączenie przy użyciu programu PowerShell zakończy się niepowodzeniem z powodu błędu: "podane poświadczenia są nieprawidłowe"
#### <a name="problem"></a>Problem
Połącz się z klastrem przy użyciu trybu zabezpieczeń "Usługi AzureActiveDirectory", po zalogowaniu pomyślnie do usługi Azure AD przy użyciu programu PowerShell, połączenie nie powiedzie się z powodu błędu: "podane poświadczenia są nieprawidłowe."

#### <a name="solution"></a>Rozwiązanie
To rozwiązanie jest taka sama jak mieszanego.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer zwróci błąd podczas logowania: "AADSTS50011"
#### <a name="problem"></a>Problem
Podczas próby Zaloguj się do usługi Azure AD w narzędziu Service Fabric Explorer strony zwraca błąd: "AADSTS50011: adres zwrotny &lt;adres url&gt; pasują do adresów odpowiedzi skonfigurowanych dla aplikacji: &lt;guid&gt;."

![Adres zwrotny SFX jest niezgodny.][sfx-reply-address-not-match]

#### <a name="reason"></a>Przyczyna
Aplikacja klastra (sieć web), która reprezentuje Service Fabric Explorer podejmuje próbę uwierzytelniania usługi Azure AD, a jako część żądania zapewnia zwrotny adres URL przekierowania. Adres URL nie jest wyświetlany w aplikacji usługi Azure AD, ale **adres URL odpowiedzi** listy.

#### <a name="solution"></a>Rozwiązanie
Wybierz opcję "Rejestracji aplikacji" na stronie usługi AAD, wybierz aplikację klastra, a następnie wybierz **adresy URL odpowiedzi** przycisku. Na stronie "Adresy URL odpowiedzi" Dodaj adres URL z Eksploratora usługi sieć szkieletowa do listy lub Zastąp jeden z elementów na liście. Po zakończeniu zapisz zmianę.

![Adres url odpowiedzi aplikacji sieci Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Połącz klaster przy użyciu uwierzytelniania usługi Azure AD za pomocą programu PowerShell
Połącz z klastrem usługi sieć szkieletowa, użyj następującego polecenia programu PowerShell:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Aby dowiedzieć się więcej na temat polecenia cmdlet Connect-ServiceFabricCluster, zobacz [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Czy można ponownie użyć tej samej dzierżawy usługi Azure AD w wielu klastrach?
Tak. Należy jednak pamiętać dodać adres URL Eksploratora sieci szkieletowej usług do aplikacji klastra (sieć web). W przeciwnym razie Eksploratora usługi sieć szkieletowa nie działa.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Dlaczego nadal należy certyfikat serwera po włączeniu usługi Azure AD?
Klienta fabricclient z rolą i FabricGateway wykonać uwierzytelnianie wzajemne. Podczas uwierzytelniania usługi Azure AD integracji z usługą Azure AD zapewnia tożsamością klienta do serwera, a certyfikat serwera jest używany do weryfikacji tożsamości serwera. Aby uzyskać więcej informacji na temat sieci szkieletowej usług certyfikatów, zobacz [certyfikatów X.509 i sieci szkieletowej usług][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz bezpiecznego klaster o udostępnienie uwierzytelniania zarządzania usługą Azure Active Directory. Następnie [połączyć się z klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak [Zarządzanie klucze tajne aplikacji](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
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

