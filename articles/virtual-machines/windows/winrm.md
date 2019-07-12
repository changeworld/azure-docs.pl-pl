---
title: Konfigurowanie dostępu WinRM na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie dostępu WinRM do użycia z maszyną wirtualną platformy Azure utworzone w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
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
ms.openlocfilehash: 2a106c639eb72d3793b0df8f4ddf36a4724f7418
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707877"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie dostępu WinRM dla maszyn wirtualnych w usłudze Azure Resource Manager

Poniżej przedstawiono kroki, które należy wykonać, aby skonfigurować Maszynę wirtualną z połączeniem WinRM

1. Tworzenie magazynu kluczy
2. Tworzenie certyfikatu z podpisem własnym
3. Przekaż certyfikat z podpisem własnym do magazynu kluczy
4. Pobierz adres URL dla Twojego certyfikatu z podpisem własnym w usłudze Key Vault
5. Odwołanie adresu URL certyfikatów z podpisem własnym podczas tworzenia maszyny Wirtualnej

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>Krok 1: Tworzenie magazynu kluczy
Możesz użyć poniższego polecenia, aby utworzyć usługę Key Vault

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
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
Przed przekazaniem certyfikatu do usługi Key Vault, utworzony w kroku 1, należy go przekonwertować do formatu, który będzie zrozumiałe dla dostawcy zasobów Microsoft.Compute. Poniżej PowerShell skrypt umożliwi, należy to zrobić

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
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: Pobierz adres URL dla Twojego certyfikatu z podpisem własnym w usłudze Key Vault
Dostawcy zasobów Microsoft.Compute musi mieć adres URL wpisu tajnego wewnątrz usługi Key Vault podczas aprowizowania maszyny Wirtualnej. Dzięki temu dostawcy zasobów Microsoft.Compute pobrać klucz tajny i utworzyć równoważne certyfikat na maszynie Wirtualnej.

> [!NOTE]
> Adres URL wpisu tajnego musi obejmować wersji, jak również. Przykładowy adres URL wygląda jak poniżej https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Szablony
Można uzyskać link do adresu URL w szablonie, używając poniższego kodu

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Możesz uzyskać ten adres URL przy użyciu poniższego polecenia programu PowerShell

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5. Odwołanie adresu URL certyfikatów z podpisem własnym podczas tworzenia maszyny Wirtualnej
#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Podczas tworzenia maszyny Wirtualnej za pomocą szablonów, certyfikat pobiera przywoływany w sekcji klucze tajne i w sekcji usługi winRM, tak jak pokazano poniżej:

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

Przykładowy szablon dla powyższych można znaleźć w tym miejscu w folderze [201-maszyny wirtualnej — winrm — keyvault — windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Kod źródłowy dla tego szablonu można znaleźć na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: Nawiązywanie połączenia z maszyną Wirtualną
Zanim będzie można połączyć z maszyną wirtualną, należy upewnić się, że komputer jest skonfigurowany do zdalnego zarządzania usługi WinRM. Uruchom program PowerShell jako administrator i wykonaj poniższe polecenie, aby upewnić się, że skonfigurowano Cię.

    Enable-PSRemoting -Force

> [!NOTE]
> Może być konieczne Sprawdź, czy Usługa WinRM jest uruchomiona, jeśli powyższe czynności zakończą się niepowodzeniem. Możesz zrobić, przy użyciu `Get-Service WinRM`
> 
> 

Po zakończeniu instalacji możesz nawiązać maszynę Wirtualną przy użyciu poniższego polecenia

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
