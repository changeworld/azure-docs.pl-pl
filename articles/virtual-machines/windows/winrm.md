---
title: Konfigurowanie dostępu usługi WinRM dla maszyny Wirtualnej platformy Azure
description: Umożliwia instalację dostępu do usługi WinRM do użytku z maszyną wirtualną platformy Azure utworzoną w modelu wdrażania Usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: 317e9376e0b8242758cd6e3f455b3f3dc9c0dc78
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879568"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie dostępu Usługi WinRM dla maszyn wirtualnych w usłudze Azure Resource Manager

Oto kroki, które należy wykonać, aby skonfigurować maszynę wirtualną z łącznością WinRM

1. Tworzenie magazynu kluczy
2. Tworzenie certyfikatu z podpisem własnym
3. Przekazywanie certyfikatu z podpisem własnym do usługi Key Vault
4. Uzyskaj adres URL certyfikatu z podpisem własnym w magazynie kluczy
5. Odwoływanie się do adresu URL certyfikatów z podpisem własnym podczas tworzenia maszyny Wirtualnej

 

## <a name="step-1-create-a-key-vault"></a>Krok 1: Tworzenie skarbca kluczy
Za pomocą poniższego polecenia można utworzyć przechowalnię kluczy

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2: Tworzenie certyfikatu z podpisem własnym
Certyfikat z podpisem własnym można utworzyć przy użyciu tego skryptu programu PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3: Przesyłanie certyfikatu z podpisem własnym do magazynu kluczy
Przed przekazaniem certyfikatu do magazynu kluczy utworzonego w kroku 1 musi on być przekonwertowany na format, który zrozumie dostawca zasobów Microsoft.Compute. Poniższy skrypt programu PowerShell pozwoli Ci to zrobić

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

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: Uzyskaj adres URL certyfikatu z podpisem własnym w magazynie kluczy
Dostawca zasobów Microsoft.Compute potrzebuje adresu URL do klucza tajnego wewnątrz magazynu kluczy podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. Dzięki temu dostawca zasobów Microsoft.Compute może pobrać klucz tajny i utworzyć równoważny certyfikat na maszynie wirtualnej.

> [!NOTE]
> Adres URL klucza tajnego musi również zawierać wersję. Przykładowy adres URL wygląda\/następująco pod adresem https: /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Szablony
Możesz uzyskać link do adresu URL w szablonie, używając poniższego kodu

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Możesz uzyskać ten adres URL za pomocą poniższego polecenia programu PowerShell

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5: Odwoływanie się do adresu URL certyfikatów z podpisem własnym podczas tworzenia maszyny Wirtualnej
#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Podczas tworzenia maszyny Wirtualnej za pomocą szablonów certyfikat jest odwoływany w sekcji wpisów tajnych i w sekcji winRM, jak poniżej:

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

Przykładowy szablon dla powyższych można znaleźć tutaj w [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Kod źródłowy tego szablonu można znaleźć w [usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: Łączenie się z maszyną wirtualną
Przed nawiązaniem połączenia z maszyną wirtualną należy upewnić się, że komputer jest skonfigurowany do zdalnego zarządzania winrm. Uruchom program PowerShell jako administrator i wykonaj poniższe polecenie, aby upewnić się, że jesteś skonfigurowany.

    Enable-PSRemoting -Force

> [!NOTE]
> Może być konieczne upewnienie się, że usługa WinRM jest uruchomiona, jeśli powyższe nie działa. Można to zrobić za pomocą`Get-Service WinRM`
> 
> 

Po zakończeniu konfiguracji można połączyć się z maszyną wirtualną za pomocą poniższego polecenia

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
