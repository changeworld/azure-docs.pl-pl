---
title: Tworzenie certyfikatu usługi Azure Key Vault | Portal Azure Marketplace
description: Wyjaśnia, jak zarejestrować Maszynę wirtualną z wirtualnego dysku twardego wdrożonych przez usługę Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 5163aa0a9195aa712fa333667b3f7ccf227469be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938401"
---
# <a name="create-certificates-for-azure-key-vault"></a>Tworzenie certyfikatów dla usługi Azure Key Vault

W tym artykule opisano sposób obsługi administracyjnej certyfikatów z podpisem własnym wymagane do nawiązania połączenia Windows Remote Management (WinRM), z maszyną wirtualną (VM) hostowanych na platformie Azure. Ten proces obejmuje trzy kroki:

1.  Utwórz certyfikat zabezpieczeń. 
2.  Tworzenie usługi Azure Key Vault do przechowywania certyfikatu. 
3.  Store certyfikatów do tego magazynu kluczy. 

Dla tej pracy można użyć nowej lub istniejącej grupy zasobów platformy Azure.  Wcześniejsze podejście jest używany w wyjaśnienia poniżej.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Tworzenie certyfikatu

Poddaj edycji, a następnie uruchom następujący skrypt programu Azure Powershell w celu utworzenia pliku certyfikatu (pfx) w folderze lokalnym.  Należy zastąpić wartości dla następujących parametrów:

|  **Parametr**        |   **Opis**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Folder lokalny, aby zapisać plik pfx w celu  |
| `$location`    | Jedną z usługi Azure standard lokalizacje geograficzne  |
| `$vmName`      | Nazwa planowanej maszyny wirtualnej platformy Azure   |
| `$certname`    | Nazwa certyfikatu; musi być zgodna z w pełni kwalifikowaną nazwę domeny z planowaną maszynę Wirtualną  |
| `$certpassword` | Hasła dla certyfikatów, muszą być zgodne hasła używanego do planowaną maszynę Wirtualną  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Zachowaj tej samej sesji programu PowerShell w konsoli aktywne podczas tych kroków, aby wartości różnych parametrów zostaną zachowane.

> [!WARNING]
> Jeśli zapiszesz ten skrypt, przechowywać go tylko w bezpiecznym miejscu, ponieważ zawiera on informacje o zabezpieczeniach (hasło).


## <a name="create-the-key-vault"></a>Tworzenie magazynu kluczy

Skopiuj zawartość [Szablon wdrożenia usługi key vault](./cpp-key-vault-deploy-template.md) do pliku na komputerze lokalnym. (w poniższym skrypcie przykład ten zasób jest `C:\certLocation\keyvault.json`.)  Poddaj edycji, a następnie uruchom następujący skrypt programu Azure Powershell, aby utworzyć wystąpienie usługi Azure Key Vault i skojarzonej grupy zasobów.  Należy zastąpić wartości dla następujących parametrów:

|  **Parametr**        |   **Opis**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Dowolnego ciągu numerycznego dołączany do identyfikatorów wdrożenia                     |
| `$rgName`             | Nazwa grupy (RG) zasobów platformy Azure do utworzenia                                        |
|  `$location`          | Jedną z usługi Azure standard lokalizacje geograficzne                                  |
| `$kvTemplateJson`     | Ścieżka pliku (keyvault.json) zawiera szablon usługi Resource Manager dla usługi key vault |
| `$kvname`             | Nazwa nowego magazynu kluczy                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Store certyfikatu

Teraz można przechowywać certyfikaty, zawarte w pliku PFX, do nowego magazynu kluczy, uruchamiając poniższy skrypt. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
    "@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Kolejne kroki

Następnie zostanie [wdrożyć Maszynę wirtualną z obrazu maszyny Wirtualnej użytkownika](./cpp-deploy-vm-user-image.md).
