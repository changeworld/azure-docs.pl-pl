---
title: Wdrażanie klastra sieci szkieletowej usług zabezpieczonych w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć klaster sieci szkieletowej usług zabezpieczonych w stosie Azure
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
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Wdrażanie klastra usługi sieć szkieletowa usług Azure stosu

Użyj **klastra sieci szkieletowej usług** elementu z portalu Azure Marketplace do wdrożenia klastra sieci szkieletowej usług zabezpieczonych w stosie Azure. 

Aby uzyskać więcej informacji na temat pracy z sieci szkieletowej usług, zobacz [Omówienie usługi Azure Service Frabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) i [scenariusze zabezpieczeń klastra sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), w dokumentacji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe elementy są wymagane do wdrożenia klastra sieci szkieletowej usług:
1. **Certyfikat klastra**  
   Jest to certyfikat serwera X.509 dodane do parametru KeyVault podczas wdrażania sieci szkieletowej usług. 
   - **CN** tego certyfikatu musi odpowiadać pełni kwalifikowanej domeny nazwę (FQDN) tworzenia klastra usługi sieć szkieletowa usług. 
   - Format certyfikatu musi być PFX, ponieważ wymagane są klucze publiczne i prywatne. 
   Zobacz [wymagania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) do tworzenia tego certyfikatu po stronie serwera.

    > [!NOTE]  
    > Inplace certyfikatu z podpisem własnym, certyfikatu x.509, serwera służy do celów testowych. Certyfikaty z podpisem własnym musi być zgodny z nazwą FQDN klastra.

2.  **Certyfikat klienta administrator** jest certyfikat, który będzie używany przez klienta do uwierzytelniania w klastrze usługi sieć szkieletowa, może być podpisany. Zobacz [wymagania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) do tworzenia tego certyfikatu klienta.

3.  **Następujące elementy muszą być dostępne w portalu Azure Marketplace stosu:**
     - **Windows Server 2016** — szablon używa obrazu systemu Windows Server 2016 w celu utworzenia klastra.  
     - **Rozszerzenie skryptu klienta** — rozszerzenie maszyny wirtualnej firmy Microsoft.  
     - **Konfiguracja etap żądanego programu PowerShell** — rozszerzenie maszyny wirtualnej firmy Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault
Aby wdrożyć klaster sieci szkieletowej usług, należy określić poprawną KeyVault *identyfikator klucza tajnego* lub adres URL klastra sieci szkieletowej usług. Szablon usługi Azure Resource Manager pobiera KeyVault jako dane wejściowe, a następnie pobierze certyfikatu klastra podczas instalacji klastra sieci szkieletowej usług. 

> [!IMPORTANT]  
> Aby dodać klucz tajny do parametru KeyVault do użycia z usługi Service Fabric, należy użyć programu PowerShell. Nie należy używać portalu.  

Użyj następującego skryptu, aby utworzyć KeyVault i dodać *certyfikatu klastra* do niego. (Zobacz [wymagania wstępne](#prerequisites).) Przed uruchomieniem skryptu Przejrzyj przykładowy skrypt i zaktualizować wskazanych parametrów do danego środowiska. Ten skrypt zostanie również output wartości, które należy podać do szablonu usługi Azure Resource Manager. 

> [!TIP]  
> Zanim będzie możliwe skryptu, musi być publicznej oferty, która obejmuje usługi dla zasobów obliczeniowych, sieci, magazynu i KeyVault. 

  ```PowerShell
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


Aby uzyskać więcej informacji, zobacz [Zarządzanie KeyVault na stosie Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Wdrażanie elementu portalu Marketplace

1. W portalu użytkowników, przejdź do **nowy** > **obliczeniowe** > **klastra sieci szkieletowej usług**. 

   ![Wybierz klastra sieci szkieletowej usług](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Dla każdej strony takich jak *podstawy*, wypełnij formularz wdrożenia. Użyj wartości domyślnych, jeśli nie masz pewności wartości. Wybierz **OK** aby przejść do następnej strony:

   ![Podstawy](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. Na *ustawienia sieciowe* , można określić określonych portów w celu otwarcia aplikacji:

   ![Ustawienia sieci](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. Na *zabezpieczeń* Dodaj wartości otrzymanych [tworzenie Azure KeyVault](#add-a-secret-to-key-vault) i przekazać klucz tajny.

   Dla *odcisk palca certyfikatu klienta administrator*, wprowadź odcisk palca *certyfikatu klienta administrator*. (Zobacz [wymagania wstępne](#prerequisites).)
   
   - Źródłowy magazyn kluczy: Określ cały *identyfikator keyVault* ciąg z wyników skryptu. 
   - Adres URL certyfikatu klastra: Określ cały adres URL z *identyfikator klucza tajnego* z wyników skryptu. 
   - Klaster odcisk palca certyfikatu: Określ *odcisk palca certyfikatu klastra* z wyników skryptu.
   - Odciski palców certyfikatów klienta administracyjnego: Określ *odcisk palca certyfikatu klienta administrator* zostały utworzone w wymaganiach wstępnych. 

   ![Dane wyjściowe skryptu](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Bezpieczeństwo](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Ukończ pracę kreatora, a następnie wybierz **Utwórz** do wdrożenia klastra sieci szkieletowej usług.



## <a name="access-the-service-fabric-cluster"></a>Dostęp do klastra sieci szkieletowej usług
Można uzyskać dostępu do klastra sieci szkieletowej usług za pomocą Eksploratora usługi sieć szkieletowa lub usługa sieci szkieletowej w programie PowerShell.


### <a name="use-service-fabric-explorer"></a>Użyj Eksploratora usługi sieć szkieletowa
1.  Sprawdź, czy przeglądarki sieci Web ma dostęp do swojego administratora certyfikatu klienta i mogą uwierzytelniać do klastra usługi sieć szkieletowa usług.  

    a. Otwórz program Internet Explorer i przejdź do **Opcje internetowe** > **zawartości** > **certyfikaty**.
  
    b. Na certyfikatach, wybierz **importu** uruchomić *Kreatora importu certyfikatów*, a następnie kliknij przycisk **dalej**. Na *plik do zaimportowania* kliknij **Przeglądaj**i wybierz **certyfikatu klienta administrator** dostarczone do szablonu usługi Azure Resource Manager.
        
       > [!NOTE]  
       > Ten certyfikat nie jest certyfikat klastra, który został wcześniej dodane do parametru KeyVault.  

    c. Upewnij się, że masz "Wymiana informacji osobistych" wybrane na liście rozwijanej rozszerzenia okna Eksploratora plików.  

       ![Wymiana informacji osobistych](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Na *magazyn certyfikatów* wybierz pozycję **osobistych**, a następnie Zakończ pracę kreatora.  
       ![Magazyn certyfikatów](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. Aby znaleźć nazwę FQDN klastra sieci szkieletowej usług:  

    a. Przejdź do grupy zasobów, który jest skojarzony z sieci szkieletowej usług klastra, a następnie zlokalizuj *publicznego adresu IP* zasobów. Wybierz obiekt skojarzony z adresem publicznego adresu IP, aby otworzyć *publicznego adresu IP* bloku.  

      ![Publiczny adres IP](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. W bloku adres publiczny adres IP, nazwa FQDN będzie wyświetlany jako *nazwy DNS*.  

      ![Nazwa DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Aby znaleźć adres URL Eksploratora usługi sieć szkieletowa i punktu końcowego połączenia klienta, przejrzyj wyniki wdrożenia szablonu.

4. W przeglądarce przejdź do https://*FQDN*: 19080. Zastąp *FQDN* z nazwą FQDN klastra sieci szkieletowej usług w kroku 2.   
   Jeśli używano certyfikatu z podpisem własnym, zostanie wyświetlone ostrzeżenie, połączenie nie jest bezpieczne. Aby przejść do witryny sieci web, wybierz **więcej informacji o**, a następnie **przejdź do strony sieci Web**. 

5. Aby uwierzytelniać się na stronie należy wybrać certyfikat do użycia. Wybierz **więcej opcji**, wybierz odpowiedni certyfikat, a następnie kliknij przycisk **OK** nawiązać połączenia z programem Service Fabric Explorer. 

   ![Uwierzytelnianie](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Za pomocą programu PowerShell sieci szkieletowej usług

1. Zainstaluj *zestawu SDK usługi Microsoft Azure Service Fabric* z [przygotowywanie środowiska projektowego w systemie Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) w dokumentacji usługi Azure Service Fabric.  

2. Po zakończeniu instalacji skonfiguruj zmiennych środowiskowych systemu, aby upewnić się, że polecenia cmdlet usługi sieć szkieletowa usług są dostępne z programu PowerShell.  
    
    a. Przejdź do **Panelu sterowania** > **System i zabezpieczenia** > **systemu**, a następnie wybierz **Zaawansowane ustawienia systemu**.  
    
      ![Panel sterowania](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Na **zaawansowane** karcie *właściwości systemu*, wybierz pozycję **zmiennych środowiskowych**.  

    c. Dla *zmienne systemowe*, Edytuj **ścieżki** i upewnij się, że **C:\\Program Files\\sieć szkieletowa usług Microsoft\\bin\\sieci szkieletowej \\Fabric.Code** znajduje się na górze listy zmiennych środowiskowych.  

      ![Listy zmiennych środowiska](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. Po zmianie kolejności zmiennych środowiskowych, ponowne uruchomienie programu PowerShell, a następnie uruchom następujący skrypt programu PowerShell w celu uzyskania dostępu do klastra usługi sieć szkieletowa usług:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > Brak nie *https://* przed nazwą klastra w skrypcie. Port 19000 jest wymagany.
 
