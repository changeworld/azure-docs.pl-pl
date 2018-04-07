---
title: Konfigurowanie dostępu do usługi WinRM dla maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Konfiguracja dostępu do usługi WinRM do użytku z maszyny wirtualnej platformy Azure utworzonych w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 5fa82dd4a85ff2e62848df0fdc6006922005a84b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie usługi WinRM dostępu dla maszyn wirtualnych w usłudze Azure Resource Manager
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>Usługa WinRM w programie vs zarządzania usługą Azure usługi Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

* Omówienie programu Menedżera zasobów Azure, zobacz to [artykułu](../../azure-resource-manager/resource-group-overview.md)
* Różnice między zarządzania usługą Azure i usługi Azure Resource Manager, zobacz to [artykułu](../../resource-manager-deployment-model.md)

Najważniejsza różnica podczas ustawiania konfiguracji usługi WinRM między dwoma stosy polega na tym, jak certyfikatu są instalowane na Maszynie wirtualnej. W stosie usługi Azure Resource Manager certyfikaty są modelowane jako zasobów zarządzanych przez dostawcę zasobów magazynu kluczy. W związku z tym użytkownik musi podać własne certyfikatów i przekaż go do magazynu kluczy przed jego użyciem w maszynie Wirtualnej.

Poniżej przedstawiono kroki, które należy wykonać, aby skonfigurować Maszynę wirtualną z połączeniem WinRM

1. Tworzenie magazynu kluczy
2. Utwórz certyfikat z podpisem własnym
3. Przekaż certyfikat z podpisem własnym do magazynu kluczy
4. Pobierz adres URL dla Twojego certyfikatu z podpisem własnym w magazynie kluczy
5. Odwołanie adres URL certyfikaty z podpisem własnym podczas tworzenia maszyny Wirtualnej

## <a name="step-1-create-a-key-vault"></a>Krok 1: Tworzenie magazynu kluczy
Można użyć poniższych polecenie, aby utworzyć magazyn kluczy

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2: Tworzenie certyfikatu z podpisem własnym
Można utworzyć certyfikatu z podpisem własnym za pomocą tego skryptu PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3: Przekaż certyfikat z podpisem własnym do magazynu kluczy
Przed przekazaniem certyfikat w magazynie kluczy utworzonego w kroku 1, należy go przekonwertować na format zrozumiałą dostawcy zasobów Microsoft.Compute. Poniżej PowerShell skrypt pozwoli, można to zrobić

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: Uzyskać adres URL dla Twojego certyfikatu z podpisem własnym w magazynie kluczy
Dostawcy zasobów Microsoft.Compute musi mieć adres URL do klucza tajnego w magazynie kluczy podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. Dzięki temu dostawcy zasobów Microsoft.Compute pobrać klucz tajny i utworzyć równoważne certyfikatu na maszynie Wirtualnej.

> [!NOTE]
> Adres URL klucz tajny musi obejmować także wersji. Przykładowy adres URL wygląda jak poniżej https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Szablony
Możesz uzyskać łącze do adresu URL za pomocą szablonu poniższego kodu

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Można uzyskać przy użyciu tego adresu URL poniżej polecenia programu PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5: Odwołanie adres URL certyfikaty z podpisem własnym podczas tworzenia maszyny Wirtualnej
#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Podczas tworzenia maszyny Wirtualnej za pomocą szablonów, certyfikat pobiera odwołanie do sekcji kluczy tajnych i sekcji usługi winRM, jak pokazano poniżej:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Przykładowy szablon dla powyższych można znaleźć tutaj na [201 vm-winrm-keyvault — windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Kod źródłowy dla tego szablonu można znaleźć w [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: Nawiązywanie połączenia z maszyną Wirtualną
Zanim będzie można połączyć się z maszyną Wirtualną, należy się upewnić, komputer jest skonfigurowany do zdalnego zarządzania usługi WinRM. Uruchom program PowerShell jako administrator i wykonywanie poniższych poleceń, aby upewnić się, w przypadku konfigurowania.

    Enable-PSRemoting -Force

> [!NOTE]
> Konieczne może być upewnij się, że Usługa WinRM jest uruchomiona, jeśli powyższe czynności zakończą się niepowodzeniem. Możesz zrobić tego za pomocą `Get-Service WinRM`
> 
> 

Po zakończeniu instalacji możesz nawiązać połączenie maszynę Wirtualną przy użyciu poniższych poleceń

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
