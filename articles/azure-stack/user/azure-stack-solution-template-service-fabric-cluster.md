---
title: Wdrażanie zabezpieczonego klastra usługi Service Fabric w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć klaster usługi Service Fabric zabezpieczone w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 0a24228f1437fbb23c11070b3e99cea60489c071
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264992"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Wdrażanie klastra usługi Service Fabric w usłudze Azure Stack

Użyj **klastra usługi Service Fabric** elementu w witrynie Azure Marketplace, aby wdrożyć klaster usługi Service Fabric zabezpieczone w usłudze Azure Stack. 

Aby uzyskać więcej informacji na temat pracy z usługą Service Fabric, zobacz [Omówienie usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) i [scenariusze zabezpieczeń klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), w dokumentacji platformy Azure.

Dostawca zasobów Microsoft.ServiceFabric nie korzysta z klastra usługi Service Fabric w usłudze Azure Stack. Zamiast klastra usługi Service Fabric w usłudze Azure Stack jest maszyny wirtualnej zestawu skalowania z zestawu wstępnie zainstalowane oprogramowanie przy użyciu Desired State Configuration (DSC).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące czynności są wymagane do wdrożenia klastra usługi Service Fabric:
1. **Certyfikat klastra**  
   Jest to certyfikat X.509, dodanych do magazynu kluczy, podczas wdrażania usługi Service Fabric. 
   - **CN** tego certyfikatu musi odpowiadać w pełni kwalifikowanej domeny nazwę (FQDN) klastra usługi Service Fabric, możesz utworzyć. 
   - Format certyfikatu musi być PFX, ponieważ wymagane są klucze publiczne i prywatne. 
     Zobacz [wymagania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) do tworzenia tego certyfikatu po stronie serwera.

     > [!NOTE]  
     > Inplace certyfikatu z podpisem własnym x.509 certyfikatu serwera służy do celów testowych. Certyfikaty z podpisem własnym nie muszą odpowiadać nazwie FQDN klastra.

1. **Certyfikat klienta administracyjnego** jest to certyfikat, który będzie używany przez klienta do uwierzytelniania w klastrze usługi Service Fabric, co może być z podpisem własnym. Zobacz [wymagania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) do tworzenia tego certyfikatu klienta.

1. **Następujące elementy muszą być dostępne w witrynie Azure Marketplace stosu:**
    - **System Windows Server 2016** — szablon używa obrazu systemu Windows Server 2016 w celu utworzenia klastra.  
    - **Rozszerzenie niestandardowego skryptu** — rozszerzenie maszyny wirtualnej od firmy Microsoft.  
    - **PowerShell Desired Configuration etapu** — rozszerzenie maszyny wirtualnej od firmy Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault
Aby wdrożyć klaster usługi Service Fabric, należy określić poprawny magazyn kluczy *identyfikator wpisu tajnego* lub adres URL dla klastra usługi Service Fabric. Szablon usługi Azure Resource Manager ma KeyVault jako dane wejściowe. Następnie szablon pobiera certyfikat klastra podczas instalowania klastra usługi Service Fabric.

> [!IMPORTANT]  
> Należy dodać wpis tajny do magazynu kluczy do użycia z usługą Service Fabric za pomocą programu PowerShell. Nie należy używać portalu.  

Użyj następującego skryptu, aby utworzyć magazyn kluczy i dodać *certyfikatu klastra* do niego. (Zobacz [wymagania wstępne](#prerequisites).) Przed uruchomieniem skryptu Przejrzyj przykładowy skrypt, a następnie zaktualizuj parametry wskazany pod kątem danego środowiska. Ten skrypt również danych wyjściowych dla wartości, które należy podać do szablonu usługi Azure Resource Manager. 

> [!TIP]  
> Zanim będzie możliwe skryptu, musi być publiczny oferta, która obejmuje usługi zasobów obliczeniowych, sieci, magazynu i magazynu kluczy. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Aby uzyskać więcej informacji, zobacz [Zarządzanie magazynu kluczy w usłudze Azure Stack przy użyciu programu PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-key-vault-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Wdrażanie elementu portalu Marketplace

1. W portalu użytkowników przejdź do **+ Utwórz zasób** > **obliczenia** > **klastra usługi Service Fabric**. 

   ![Wybierz klaster usługi Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. Dla każdej strony takich jak *podstawy*, wypełnij formularz wdrożenia. Jeśli nie masz pewności, wartości, należy użyć wartości domyślnych. Wybierz **OK** aby przejść do następnej strony:

   ![Podstawy](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. Na *ustawienia sieciowe* strony, można określić określone porty otworzyć dla swojej aplikacji:

   ![Ustawienia sieciowe](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. Na *zabezpieczeń* strony, Dodaj wartości, które masz od [tworzenia magazynu kluczy Azure](#add-a-secret-to-key-vault) i przekazywanie klucza tajnego.

   Aby uzyskać *odcisk palca certyfikatu klienta administrator*, wprowadź odcisk palca *certyfikat klienta administracyjnego*. (Zobacz [wymagania wstępne](#prerequisites).)
   
   - Źródłowy magazyn kluczy:  Określ całą *identyfikator magazynu kluczy* ciąg z wyniki skryptu. 
   - Adres URL certyfikatu klastra: Określ cały adres URL z *identyfikator wpisu tajnego* z wyników skryptu. 
   - Odcisk palca certyfikatu klastra: Określ *odcisk palca certyfikatu klastra* z wyników skryptu.
   - Klient administracyjny odcisków palca certyfikatu: Określ *odcisk palca certyfikatu klienta administrator* utworzone w wymaganiach wstępnych. 

   ![Dane wyjściowe skryptu](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Bezpieczeństwo](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Ukończ pracę kreatora, a następnie wybierz **Utwórz** wdrożyć klaster usługi Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Dostęp do klastra usługi Service Fabric
Za dostęp do klastra usługi Service Fabric przy użyciu narzędzia Service Fabric Explorer lub Service Fabric programu PowerShell.


### <a name="use-service-fabric-explorer"></a>Use Service Fabric Explorer
1.  Sprawdź, czy przeglądarka sieci Web ma dostęp do Twój certyfikat klienta administracyjnego i może wykonać uwierzytelnienie do klastra usługi Service Fabric.  

    a. Otwórz program Internet Explorer i przejdź do **Opcje internetowe** > **zawartości** > **certyfikaty**.
  
    b. Na certyfikatach, wybierz **importu** można uruchomić *Kreatora importu certyfikatów*, a następnie kliknij przycisk **dalej**. Na *plik do zaimportowania* kliknij **Przeglądaj**i wybierz **certyfikat klienta administracyjnego** dostarczane z szablonem usługi Azure Resource Manager.
        
       > [!NOTE]  
       > Ten certyfikat nie jest certyfikat klastra, który wcześniej został dodany do magazynu kluczy.  

    c. Upewnij się, że masz "Wymiana informacji osobistych" wybrana na liście rozwijanej rozszerzenia w oknie Eksploratora plików.  

       ![Wymiana informacji osobistych](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Na *Store certyfikatu* wybierz opcję **osobistych**, a następnie ukończ jego pracę.  
       ![Magazyn certyfikatów](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Aby znaleźć nazwę FQDN klastra usługi Service Fabric:  

    a. Przejdź do grupy zasobów, który jest skojarzony z usługą Service Fabric klaster, a następnie zlokalizuj *publiczny adres IP* zasobów. Wybierz obiekt skojarzonych z tym adresem publicznym adresem IP, aby otworzyć *publiczny adres IP* bloku.  

      ![Publiczny adres IP](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. W bloku adres publiczny adres IP, nazwy FQDN jest wyświetlany jako *nazwy DNS*.  

      ![Nazwa DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Aby znaleźć adres URL narzędzia Service Fabric Explorer, a punkt końcowy połączenia klienta, należy przejrzeć wyniki wdrażania szablonu.

1. W przeglądarce przejdź do https://*FQDN*: 19080. Zastąp *FQDN* nazwą FQDN klastra usługi Service Fabric w kroku 2.   
   Jeśli używano certyfikatu z podpisem własnym, otrzymasz ostrzeżenie, że połączenie nie jest bezpieczne. Aby przejść do witryny sieci web, wybierz **więcej informacji o**, a następnie **przejdź do strony sieci Web**. 

1. Aby uwierzytelniać się do witryny, należy wybrać certyfikat do użycia. Wybierz **więcej opcji**, wybierz odpowiedni certyfikat, a następnie kliknij **OK** połączyć się z narzędzia Service Fabric Explorer. 

   ![Uwierzytelnianie](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Użyj programu PowerShell usługi Service Fabric

1. Zainstaluj *zestawu SDK usługi Microsoft Azure Service Fabric* z [przygotowanie środowiska projektowego w Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) w dokumentacji usługi Azure Service Fabric.  

1. Po zakończeniu instalacji należy skonfigurować zmienne środowiskowe systemu, aby upewnić się, że polecenia cmdlet usługi Service Fabric są dostępne z poziomu programu PowerShell.  
    
    a. Przejdź do **Panelu sterowania** > **System i zabezpieczenia** > **systemu**, a następnie wybierz pozycję **Zaawansowane ustawienia systemu**.  
    
      ![Panel sterowania](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Na **zaawansowane** karcie *właściwości systemu*, wybierz opcję **zmienne środowiskowe**.  

    c. Dla *zmiennych systemowych*, Edytuj **ścieżki** i upewnij się, że **C:\\Program Files\\Microsoft Service Fabric\\bin\\sieci szkieletowej \\Fabric.Code** u góry listy zmiennych środowiskowych.  

      ![Lista zmiennych środowiskowych](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Po zmianie kolejności zmiennych środowiskowych, ponownie uruchom program PowerShell, a następnie uruchom poniższy skrypt programu PowerShell w celu uzyskania dostępu do klastra usługi Service Fabric:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > Istnieje nie *https://* przed nazwą klastra w skrypcie. Port 19000 jest wymagany.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie rozwiązania Kubernetes do usługi Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
