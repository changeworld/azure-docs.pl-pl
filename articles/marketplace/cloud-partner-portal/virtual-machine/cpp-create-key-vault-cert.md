---
title: Tworzenie certyfikatu usługi Azure Key Vault | Azure Marketplace
description: W tym artykule wyjaśniono, jak zarejestrować maszynę wirtualną z dysku wirtualnego wdrożonego w usłudze Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: dc106ecf40367172f28c0f339bbcce8bddb50a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278079"
---
# <a name="create-certificates-for-azure-key-vault"></a>Tworzenie certyfikatów dla usługi Azure Key Vault

W tym artykule wyjaśniono, jak aprowizować certyfikaty z podpisem własnym wymagane do ustanowienia łączności usługi WinRM (Windows Remote Management) z maszyną wirtualną hostowanego przez platformę Azure(VM). Proces ten składa się z trzech etapów:

1.  Utwórz certyfikat zabezpieczeń. 
2.  Utwórz usługę Azure Key Vault do przechowywania tego certyfikatu. 
3.  Przechowuj certyfikaty w tym magazynie kluczy. 

Można użyć nowej lub istniejącej grupy zasobów platformy Azure dla tej pracy.  Pierwsze podejście jest używane w poniższym wyjaśnieniu.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Tworzenie certyfikatu

Edytuj i uruchom następujący skrypt programu Azure Powershell, aby utworzyć plik certyfikatu (pfx) w folderze lokalnym.  Należy zastąpić wartości dla następujących parametrów:

|  **Parametr**        |   **Opis**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Folder lokalny do zapisywania pliku .pfx w  |
| `$location`    | Jedna ze standardowych lokalizacji geograficznych platformy Azure  |
| `$vmName`      | Nazwa planowanej maszyny wirtualnej platformy Azure   |
| `$certname`    | Nazwa certyfikatu; musi być zgodna z w pełni kwalifikowaną nazwą domeny planowanej maszyny Wirtualnej  |
| `$certpassword` | Hasło certyfikatów musi być zgodne z hasłem używanym dla planowanej maszyny Wirtualnej  |
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
> Podczas tych czynności zachowaj aktywną tę samą sesję konsoli programu PowerShell, aby zachować wartości różnych parametrów.

> [!WARNING]
> Jeśli zapiszesz ten skrypt, przechowuj go tylko w bezpiecznej lokalizacji, ponieważ zawiera informacje zabezpieczające (hasło).


## <a name="create-the-key-vault"></a>Tworzenie magazynu kluczy

Skopiuj zawartość [szablonu wdrażania magazynu kluczy](./cpp-key-vault-deploy-template.md) do pliku na komputerze lokalnym. (w poniższym przykładowym skrypcie ten zasób to `C:\certLocation\keyvault.json`.)  Edytuj i uruchom następujący skrypt programu Azure Powershell, aby utworzyć wystąpienie usługi Azure Key Vault i skojarzoną grupę zasobów.  Należy zastąpić wartości dla następujących parametrów:

|  **Parametr**        |   **Opis**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Dowolny ciąg numeryczny dołączony do identyfikatorów wdrażania                     |
| `$rgName`             | Nazwa grupy zasobów platformy Azure (RG) do utworzenia                                        |
|  `$location`          | Jedna ze standardowych lokalizacji geograficznych platformy Azure                                  |
| `$kvTemplateJson`     | Ścieżka pliku (keyvault.json) zawierająca szablon Menedżera zasobów dla magazynu kluczy |
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

## <a name="store-the-certificate"></a>Przechowywanie certyfikatu

Teraz można przechowywać certyfikaty zawarte w pliku pfx w nowym magazynie kluczy, uruchamiając następujący skrypt. 

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Następne kroki

Następnie wdrożysz [maszynę wirtualną z obrazu maszyny Wirtualnej użytkownika](./cpp-deploy-vm-user-image.md).
