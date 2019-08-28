---
title: Rozszerzenie DSC platformy Azure dla systemu Linux
description: Instaluje pakiety OMI i DSC umożliwiające skonfigurowanie konfiguracji żądanego stanu przy użyciu maszyny wirtualnej z systemem Linux platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: c37b81e08e5d9f150081a9dc12af51175e3f590c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084719"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozszerzenie DSC dla systemu Linux (Microsoft. OSTCExtensions. DSCForLinux)

Konfiguracja żądanego stanu (DSC) to platforma zarządzania, która umożliwia zarządzanie infrastrukturą IT i programistyczną przy użyciu konfiguracji jako kodu.

Rozszerzenie DSCForLinux jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta OMI i DSC na maszynach wirtualnych platformy Azure. Rozszerzenie DSC może również wykonywać następujące akcje


- Zarejestruj maszynę wirtualną z systemem Linux, aby Azure Automation konto w celu ściągania konfiguracji z usługi Azure Automation (Register ExtensionAction)
- Wypychanie konfiguracji MOF do maszyny wirtualnej z systemem Linux (wypychanie ExtensionAction)
- Zastosuj konfigurację meta MOF do maszyny wirtualnej z systemem Linux, aby skonfigurować serwer ściągania w celu ściągnięcia konfiguracji węzła (ściąganie ExtensionAction)
- Instalowanie niestandardowych modułów DSC na maszynie wirtualnej z systemem Linux (Install ExtensionAction)
- Usuwanie niestandardowych modułów DSC do maszyny wirtualnej z systemem Linux (Usuń ExtensionAction)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie DSC Linux obsługuje wszystkie dystrybucje [systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) z wyjątkiem:

| Dystrybucja | Version |
|---|---|
| Debian | wszystkie wersje |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie DSCForLinux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. Na przykład rozszerzenie Register wymaga łączności z usługą Automation. W przypadku innych akcji, takich jak ściąganie, instalacja wymaga łączności z usługą Azure Storage/GitHub. Zależy to od ustawień dostarczonych przez klienta.

## <a name="extension-schema"></a>Schemat rozszerzenia

### <a name="11-public-configuration"></a>Konfiguracja publiczna 1,1

Oto wszystkie obsługiwane parametry konfiguracji publicznej:

* `FileUri`: (opcjonalnie, String) identyfikator URI pliku MOF/plik meta MOF/plik ZIP zasobów niestandardowych.
* `ResourceName`: (opcjonalnie, String) nazwa niestandardowego modułu zasobów
* `ExtensionAction`: (opcjonalnie, ciąg) określa, jakie jest rozszerzenie. prawidłowe wartości: Zarejestruj, wypchnij, Pobierz, Zainstaluj, Usuń. Jeśli nie zostanie określony, jest traktowany jako akcja push domyślnie.
* `NodeConfigurationName`: (opcjonalnie, ciąg) nazwa konfiguracji węzła do zastosowania.
* `RefreshFrequencyMins`: (opcjonalnie, int) określa, jak często (w minutach) próbuje uzyskać konfigurację z serwera ściągania. 
       Jeśli konfiguracja na serwerze ściągania różni się od bieżącej w węźle docelowym, zostanie ona skopiowana do oczekującego magazynu i zastosowana.
* `ConfigurationMode`: (opcjonalnie, ciąg) określa sposób zastosowania konfiguracji przez DSC. Prawidłowe wartości to: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcjonalnie, int) określa, jak często (w minutach) DSC gwarantuje, że konfiguracja jest w żądanym stanie.

> [!NOTE]
> Jeśli używasz wersji < 2,3, parametr mode jest taki sam jak ExtensionAction. Tryb wydaje się być przeciążonym terminem. W związku z tym, aby uniknąć pomyłek, ExtensionAction jest używany z wersji 2,3 lub nowszej. W celu zapewnienia zgodności z poprzednimi wersjami rozszerzenie obsługuje zarówno tryb, jak i ExtensionAction. 
>

### <a name="12-protected-configuration"></a>Konfiguracja chroniona 1,2

Poniżej przedstawiono wszystkie obsługiwane parametry konfiguracji chronionej:

* `StorageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu, które zawiera plik
* `StorageAccountKey`: (opcjonalnie, ciąg) klucz konta magazynu, który zawiera plik
* `RegistrationUrl`: (opcjonalnie, ciąg) adres URL konta Azure Automation
* `RegistrationKey`: (opcjonalnie, String) klucz dostępu konta Azure Automation


## <a name="scenarios"></a>Scenariusze

### <a name="register-to-azure-automation-account"></a>Zarejestruj się w usłudze Azure Automation Account
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
Public. JSON
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

format programu PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Zastosuj plik konfiguracji MOF (na koncie usługi Azure Storage) do maszyny wirtualnej

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public. JSON
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

format programu PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Zastosuj plik konfiguracji MOF (w magazynie publicznym) do maszyny wirtualnej

Public. JSON
```json
{
  "FileUri": "<mof-file-uri>"
}
```

format programu PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Zastosuj plik konfiguracji meta MOF (na koncie usługi Azure Storage) do maszyny wirtualnej

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public. JSON
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

format programu PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Zastosuj plik konfiguracji meta MOF (w magazynie publicznym) do maszyny wirtualnej
Public. JSON
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
format programu PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Instalowanie niestandardowego modułu zasobów (plik ZIP na koncie usługi Azure Storage) do maszyny wirtualnej
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
Public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

format programu PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Instalowanie niestandardowego modułu zasobów (plik ZIP w magazynie publicznym) z maszyną wirtualną
Public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
format programu PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Usuwanie niestandardowego modułu zasobów z maszyny wirtualnej
Public. JSON
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
format programu PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealnym rozwiązaniem podczas wdrażania co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu, takiej jak dołączanie do Azure Automation. 

Przykładowy szablon Menedżer zasobów to [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) i [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Aby uzyskać więcej informacji na temat szablonu Azure Resource Manager, odwiedź stronę [tworzenie Azure Resource Manager szablonów](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

### <a name="21-using-azure-cliazure-cli"></a>2.1. Korzystanie z [**interfejsu wiersza polecenia platformy Azure**] [Azure-CLI]
Przed wdrożeniem rozszerzenia DSCForLinux należy skonfigurować `public.json` i `protected.json`, zgodnie z różnymi scenariuszami w sekcji 3.

#### <a name="211-classic"></a>2.1.1. Klasyczny
Tryb klasyczny jest również nazywany trybem zarządzania usługami platformy Azure. Aby przełączyć się do niego, należy uruchomić:
```
$ azure config mode asm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając następujące narzędzia:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Aby poznać najnowszą wersję rozszerzenia, uruchom polecenie:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Aby przełączyć się do trybu Azure Resource Manager, możesz uruchomić:
```
$ azure config mode arm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając następujące narzędzia:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> W trybie `azure vm extension list` Azure Resource Manager nie jest dostępny teraz.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Korzystanie z [**Azure PowerShell**] [Azure-PowerShell]

#### <a name="221-classic"></a>2.2.1 klasyczny

Aby zalogować się do konta platformy Azure (trybu zarządzania usługami platformy Azure), należy uruchomić następujące działania:

```powershell>
Add-AzureAccount
```

I Wdróż rozszerzenie DSCForLinux, uruchamiając:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Należy zmienić zawartość $privateConfig i $publicConfig zgodnie z różnymi scenariuszami w powyższej sekcji 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="222resource-manager"></a>2.2.2. Menedżer zasobów

Aby zalogować się na koncie platformy Azure (w trybie Azure Resource Manager), należy uruchomić następujące działania:

```powershell>
Login-AzAccount
```

Kliknij [**tutaj**](../../azure-resource-manager/manage-resources-powershell.md) , aby dowiedzieć się więcej na temat używania Azure PowerShell z Azure Resource Manager.

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając następujące narzędzia:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Należy zmienić zawartość $privateConfig i $publicConfig zgodnie z różnymi scenariuszami w powyższej sekcji 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kod błędu: 51 reprezentuje nieobsługiwaną akcję rozszerzenia dystrybucji lub nieobsługiwaną.
W niektórych przypadkach rozszerzenie DSC systemu Linux nie może zainstalować OMI, gdy nowsza wersja OMI już istnieje na maszynie. [odpowiedź na błąd: (000003) Obniżanie poziomu jest niedozwolone]



### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](features-linux.md).
