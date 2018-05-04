---
title: Szyfrowanie dysków na maszynie Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zaszyfrować dyski wirtualne na maszynie Wirtualnej systemu Windows, aby zwiększyć bezpieczeństwo, przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Jak zaszyfrować dyski wirtualne na maszynie Wirtualnej systemu Windows
Ulepszone maszyny wirtualnej (VM) zabezpieczeń i zgodności mogą być szyfrowane dyski wirtualne na platformie Azure. Dyski są szyfrowane za pomocą kluczy kryptograficznych, które są już zabezpieczone w usłudze Azure Key Vault. Kontrolowanie tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Ten artykuł zawiera szczegóły dotyczące sposobu szyfrowania dysków wirtualnych w maszynie Wirtualnej z systemem Windows przy użyciu programu Azure PowerShell. Możesz również [szyfrowania maszyny Wirtualnej systemu Linux przy użyciu programu Azure CLI 2.0](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Omówienie szyfrowania dysków
Dyski wirtualne na maszynach wirtualnych systemu Windows są szyfrowane, gdy za pomocą funkcji Bitlocker. Jest bezpłatna dla szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usługi Azure Key Vault przy użyciu ochrony oprogramowania, lub możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania wirtualnych dysków dołączonych do maszyny Wirtualnej. Zachowanie kontroli nad tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Nazwy głównej usługi Azure Active Directory zapewnia mechanizm bezpiecznego do wystawiania tych kluczy kryptograficznych, ponieważ maszyny wirtualne są zasilane i wyłączanie.

Proces szyfrowania maszyny Wirtualnej jest następujący:

1. Tworzenie klucza kryptograficznego w usłudze Azure Key Vault.
2. Skonfiguruj klucza kryptograficznego, który może być używany do szyfrowania dysków.
3. Aby odczytać klucza kryptograficznego z usługi Azure Key Vault, utworzyć usługę Azure Active Directory podmiot z odpowiednimi uprawnieniami.
4. Wydaj polecenie wirtualnych dysków, określając usługi Azure Active Directory usługa podmiotu zabezpieczeń i odpowiednie klucza kryptograficznego używanego do szyfrowania.
5. Nazwy głównej usługi Azure Active Directory żądań wymaganego klucza kryptograficznego z usługi Azure Key Vault.
6. Dyski wirtualne są szyfrowane za pomocą podanego klucza kryptograficznego.

## <a name="encryption-process"></a>Proces szyfrowania
Szyfrowanie dysków opiera się na następujące dodatkowe składniki:

* **Usługa Azure Key Vault** — używane do ochrony kluczy kryptograficznych i kluczy tajnych używanych przez proces szyfrowania i odszyfrowywania dysku. 
  * Jeśli istnieje, można użyć istniejącego magazynu kluczy Azure. Nie trzeba przeznaczyć magazyn kluczy szyfrowania dysków.
  * Aby rozdzielić granice administracyjne i widoczności klucza, możesz utworzyć dedykowany magazyn kluczy.
* **Usługa Azure Active Directory** — obsługuje bezpiecznej wymiany wymagane klucze szyfrowania i uwierzytelniania dla żądanej akcji. 
  * Zazwyczaj można użyć istniejącego wystąpienia usługi Azure Active Directory, do przechowywania aplikacji.
  * Nazwy głównej usługi zapewnia mechanizm bezpiecznego do żądania i wydawane odpowiednich kluczy kryptograficznych. Nie opracowujesz aplikację rzeczywista, która integruje się z usługą Azure Active Directory.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia
Obsługiwane scenariusze i wymagania dotyczące szyfrowania dysków:

* Włączenie szyfrowania na nowych maszyn wirtualnych systemu Windows z portalu Azure Marketplace obrazy lub niestandardowego obrazu wirtualnego dysku twardego.
* Włączenie szyfrowania na istniejących maszyn wirtualnych systemu Windows na platformie Azure.
* Włączenie szyfrowania na maszynach wirtualnych systemu Windows, które są skonfigurowane przy użyciu funkcji miejsca do magazynowania.
* Wyłączenie szyfrowania systemu operacyjnego i danych dyski dla maszyn wirtualnych systemu Windows.
* Wszystkie zasoby (na przykład usługi Key Vault, konta magazynu i maszyny Wirtualnej) muszą być w tym samym regionie Azure i subskrypcji.
* Warstwy standardowa maszyn wirtualnych, takie jak, D, DS, G i GS serii maszyn wirtualnych.

Szyfrowanie dysku nie jest obecnie obsługiwane w następujących scenariuszach:

* Maszyny wirtualne warstwy podstawowa.
* Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrożenia.
* Aktualizowanie kluczy kryptograficznych w przypadku już zaszyfrowany maszyny Wirtualnej.
* Integracja z usługą zarządzania kluczami lokalnego.

## <a name="create-azure-key-vault-and-keys"></a>Tworzenie usługi Azure Key Vault i kluczy
Przed rozpoczęciem upewnij się, że masz zainstalowaną najnowszą wersję modułu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). W przykładach poleceń Zastąp wszystkie parametry przykład własne nazwy, lokalizacji i wartości klucza. W poniższych przykładach użyto Konwencji *myResourceGroup*, *myKeyVault*, *myVM*itp.

Pierwszym krokiem jest do utworzenia magazynu kluczy Azure do przechowywania kluczy kryptograficznych. Usługa Azure Key Vault można przechowywać kluczy, kluczy tajnych lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Szyfrowanie dysków wirtualnych można utworzyć magazyn kluczy do przechowywania klucza kryptograficznego, który jest używany do szyfrowania lub odszyfrowywania wirtualnych dysków. 

Włącz dostawcy usługi Azure Key Vault w ramach Twojej subskrypcji platformy Azure z [AzureRmResourceProvider rejestru](/powershell/module/azurerm.resources/register-azurermresourceprovider), następnie utwórz nową grupę zasobów o [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w *wschodnie stany USA* lokalizacji:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Usługa Azure Key Vault zawierający klucze kryptograficzne i zasoby obliczeniowe skojarzone, takie jak magazyn i samej maszyny Wirtualnej musi znajdować się w tym samym regionie. Tworzenie usługi Azure Key Vault z [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) i włączyć usługi Key Vault służących do szyfrowania dysku. Określ unikatową nazwę usługi Key Vault *keyVaultName* w następujący sposób:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Mogą być przechowywane przy użyciu oprogramowania lub sprzętu modelu zabezpieczeń (HSM) ochronę kluczy kryptograficznych. Użycie modułów HSM wymaga premium magazynu kluczy. Brak dodatkowych kosztów tworzenia premium magazynu kluczy, a nie standardowy magazyn kluczy, którego przechowuje klucze chronione przez oprogramowanie. Aby utworzyć premium magazynu kluczy, w poprzednim kroku Dodaj *- Sku "Premium"* parametrów. W poniższym przykładzie użyto klucze chronione przez oprogramowanie, ponieważ utworzono standardowe magazynu kluczy. 

W przypadku obu modeli ochrony platformy Azure musi otrzymać dostęp do żądania kluczy kryptograficznych, podczas rozruchu maszyny Wirtualnej do odszyfrowania dysków wirtualnych. Tworzenie klucza kryptograficznego w magazyn kluczy o [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). W poniższym przykładzie jest tworzony klucz o nazwie *klucze*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Tworzenie nazwy głównej usługi Azure Active Directory
Podczas dyski wirtualne są zaszyfrowanie lub odszyfrowanie, należy określić konto w celu obsługi uwierzytelniania i wymiany kluczy kryptograficznych z magazynu kluczy. To konto, nazwy głównej usługi Azure Active Directory umożliwia platformy Azure zażądać odpowiednich kluczy kryptograficznych w imieniu maszyny Wirtualnej. Domyślne wystąpienie usługi Azure Active Directory jest dostępne w Twojej subskrypcji, chociaż w wielu organizacjach są wyposażone w dedykowane katalogów usługi Azure Active Directory.

Tworzenie nazwy głównej usługi w usłudze Azure Active Directory z [AzureRmADServicePrincipal nowy](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Aby określić bezpieczne hasło, wykonaj [ograniczenia w usłudze Azure Active Directory i zasadami haseł](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Pomyślnie szyfrowania lub odszyfrowywania dysków wirtualnych, uprawnienia do klucza kryptograficznego, przechowywane w magazynie klucz musi mieć ustawioną zezwolenie na nazwę główną usługi Azure Active Directory do odczytu kluczy. Ustaw uprawnienia na magazyn kluczy o [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby przetestować proces szyfrowania, utwórz maszynę Wirtualną za pomocą [AzureRmVm nowy](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu *systemu Windows Server 2016 Datacenter* obrazu. Po wyświetleniu monitu o poświadczenia, wprowadź nazwę użytkownika i hasło do użycia dla maszyny Wirtualnej:

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Szyfrowanie maszyny wirtualnej
Do szyfrowania dysków wirtualnych, można przenosić ze sobą wszystkie poprzednie składniki:

1. Podaj nazwę główną usługi Azure Active Directory i hasło.
2. Określ Key Vault w celu przechowywania metadanych dla zaszyfrowanych dysków.
3. Określ klucze kryptograficzne służące do rzeczywistego szyfrowania i odszyfrowywania.
4. Określ, czy chcesz zaszyfrować dysk systemu operacyjnego, dyski danych lub all.

Szyfrowanie maszyny Wirtualnej z [AzureRmVMDiskEncryptionExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) przy użyciu klucza usługi Azure Key Vault i poświadczenia główne usługi Azure Active Directory. Poniższy przykład pobiera wszystkie informacje o kluczu, a następnie szyfruje maszyny Wirtualnej o nazwie *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Zaakceptować monit o kontynuowanie szyfrowania maszyny Wirtualnej. Podczas procesu ponownego uruchomienia maszyny Wirtualnej. Po zakończeniu procesu szyfrowania i uruchomieniu maszyny Wirtualnej, przejrzyj stanu szyfrowania z [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat zarządzania usługą Azure Key Vault, zobacz [Konfigurowanie magazynu kluczy dla maszyn wirtualnych](key-vault-setup.md).
* Aby uzyskać więcej informacji o szyfrowaniu dysków, takich jak przygotowywanie zaszyfrowanego niestandardowego maszyny Wirtualnej do przekazania do platformy Azure, zobacz [szyfrowania dysków Azure](../../security/azure-security-disk-encryption.md).
