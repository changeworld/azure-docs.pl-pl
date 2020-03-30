---
title: Rozszerzenie usługi Azure DSC dla systemu Linux
description: Instaluje pakiety OMI i DSC, aby umożliwić konfigurowanie maszyny wirtualnej systemu Azure Linux przy użyciu konfiguracji żądanego stanu.
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
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250623"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozszerzenie DSC dla systemu Linux (Microsoft.OSTCExtensions.DSCForLinux)

Konfiguracja żądanego stanu (DSC) to platforma zarządzania, której można używać do zarządzania infrastrukturą IT i deweloperów z konfiguracją jako kodem.

> [!NOTE]
> Rozszerzenie DSC dla systemu Linux i [rozszerzenie maszyny wirtualnej usługi Azure Monitor dla systemu Linux](/azure/virtual-machines/extensions/oms-linux) obecnie występują w konflikcie i nie są obsługiwane w konfiguracji side-by-side. Nie używaj dwóch rozwiązań razem na tej samej maszynie Wirtualnej.

Rozszerzenie DSCForLinux jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta OMI i DSC na maszynach wirtualnych platformy Azure. Rozszerzenie DSC można również wykonać następujące akcje:


- Zarejestruj maszynę wirtualną systemu Linux na koncie usługi Azure Automation, aby pobierać konfiguracje z usługi Azure Automation (Register ExtensionAction).
- Wypychaj konfiguracje MOF do maszyny Wirtualnej Systemu Linux (Push ExtensionAction).
- Zastosuj konfigurację meta MOF do maszyny Wirtualnej systemu Linux, aby skonfigurować serwer ściągania w celu wyciągnięcia konfiguracji węzła (Pull ExtensionAction).
- Zainstaluj niestandardowe moduły DSC na maszynie wirtualnej systemu Linux (Install ExtensionAction).
- Usuń niestandardowe moduły DSC z maszyny wirtualnej w systemie Linux (Usuń ExtensionAction).

 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie DSC Linux obsługuje wszystkie [dystrybucje linuksa zatwierdzone na platformie Azure](/azure/virtual-machines/linux/endorsed-distros) z wyjątkiem:

| Dystrybucja | Wersja |
|---|---|
| Debian | Wszystkie wersje |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie DSCForLinux wymaga, aby docelowa maszyna wirtualna była podłączona do Internetu. Na przykład rozszerzenie Register wymaga łączności z usługą automatyzacji. W przypadku innych akcji, takich jak Ściąganie, Ściąganie, Instalowanie wymaga łączności z usługą Azure Storage i GitHub. To zależy od ustawień dostarczonych przez klienta.

## <a name="extension-schema"></a>Schemat rozszerzenia

### <a name="public-configuration"></a>Konfiguracja publiczna

Oto wszystkie obsługiwane parametry konfiguracji publicznej:

* `FileUri`: (opcjonalnie, ciąg) Identyfikator pliku MOF, meta pliku MOF lub niestandardowego pliku zip zasobu.
* `ResourceName`: (opcjonalnie, ciąg) Nazwa niestandardowego modułu zasobów.
* `ExtensionAction`: (opcjonalnie, ciąg) Określa, co robi rozszerzenie. Prawidłowe wartości to Register, Push, Pull, Install i Remove. Jeśli nie zostanie określony, jest domyślnie uważany za akcję wypychania.
* `NodeConfigurationName`: (opcjonalnie, ciąg) Nazwa konfiguracji węzła do zastosowania.
* `RefreshFrequencyMins`: (opcjonalnie, int) Określa, jak często (w minutach) DSC próbuje uzyskać konfigurację z serwera ściągania. 
       Jeśli konfiguracja na serwerze ściągania różni się od bieżącej w węźle docelowym, jest kopiowana do oczekującego magazynu i stosowana.
* `ConfigurationMode`: (opcjonalnie, ciąg) Określa, jak DSC należy zastosować konfigurację. Prawidłowe wartości to ApplyOnly, ApplyAndMonitor i ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcjonalnie, int) Określa, jak często (w minutach) DSC zapewnia, że konfiguracja jest w żądanym stanie.

> [!NOTE]
> Jeśli używasz wersji wcześniejszej niż 2.3, parametr mode jest taki sam jak ExtensionAction. Tryb wydaje się być przeciążonym terminem. Aby uniknąć nieporozumień, ExtensionAction jest używany od wersji 2.3. W celu zapewnienia zgodności z powrotem rozszerzenie obsługuje zarówno tryb, jak i ExtensionAction. 
>

### <a name="protected-configuration"></a>Konfiguracja chroniona

Oto wszystkie obsługiwane chronione parametry konfiguracji:

* `StorageAccountName`: (opcjonalnie, ciąg) Nazwa konta magazynu zawierającego plik
* `StorageAccountKey`: (opcjonalnie, ciąg) Klucz konta magazynu zawierającego plik
* `RegistrationUrl`: (opcjonalnie, ciąg) Adres URL konta usługi Azure Automation
* `RegistrationKey`: (opcjonalnie, ciąg) Klucz dostępu konta usługi Azure Automation


## <a name="scenarios"></a>Scenariusze

### <a name="register-an-azure-automation-account"></a>Rejestrowanie konta usługi Azure Automation
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Format programu PowerShell
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Stosowanie pliku konfiguracyjnego MOF (na koncie magazynu platformy Azure) do maszyny Wirtualnej

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Format programu PowerShell
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Stosowanie pliku konfiguracyjnego MOF (w magazynie magazynowym) do maszyny Wirtualnej

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Format programu PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Stosowanie pliku konfiguracyjnego meta MOF (na koncie magazynu platformy Azure) do maszyny Wirtualnej

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Format programu PowerShell
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Stosowanie pliku konfiguracyjnego meta MOF (w magazynie magazynowym) do maszyny Wirtualnej
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Format programu PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instalowanie niestandardowego modułu zasobów (pliku zip na koncie magazynu platformy Azure) na maszynie Wirtualnej
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Format programu PowerShell
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instalowanie niestandardowego modułu zasobów (pliku zip w magazynie publicznym) na maszynie Wirtualnej
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Format programu PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Usuwanie niestandardowego modułu zasobów z maszyny Wirtualnej
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Format programu PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Szablony są idealne podczas wdrażania co najmniej jednej maszyny wirtualnej, które wymagają konfiguracji po wdrożeniu, takich jak dołączanie do usługi Azure Automation. 

Przykładowy szablon Menedżera zasobów to [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) i [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Aby uzyskać więcej informacji na temat szablonu usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

### <a name="use-azure-cliazure-cli"></a>Użyj polecenia [Azure CLI][azure-cli]
Przed wdrożeniem rozszerzenia DSCForLinux należy `public.json` `protected.json` skonfigurować i zgodnie z różnymi scenariuszami w sekcji 3.

#### <a name="classic"></a>Wdrożenie klasyczne

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Klasyczny tryb wdrażania jest również nazywany trybem zarządzania usługami Azure. Możesz przełączyć się do niego, uruchamiając:
```
$ azure config mode asm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Aby poznać najnowszą dostępną wersję rozszerzenia, uruchom:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Możesz przełączyć się do trybu Usługi Azure Resource Manager, uruchamiając:
```
$ azure config mode arm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> W trybie usługi `azure vm extension list` Azure Resource Manager nie jest na razie dostępna.
>

### <a name="use-azure-powershellazure-powershell"></a>Użyj programu [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Wdrożenie klasyczne

Możesz zalogować się do konta platformy Azure w trybie zarządzania usługami platformy Azure, uruchamiając:

```powershell>
Add-AzureAccount
```

I wdrożyć rozszerzenie DSCForLinux, uruchamiając:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Zmień zawartość $privateConfig i $publicConfig według różnych scenariuszy w poprzedniej sekcji.
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

#### <a name="resource-manager"></a>Resource Manager

Możesz zalogować się do konta platformy Azure w trybie usługi Azure Resource Manager, uruchamiając:

```powershell>
Login-AzAccount
```

Aby dowiedzieć się więcej na temat korzystania z usługi Azure PowerShell z usługą Azure Resource Manager, zobacz [Zarządzanie zasobami platformy Azure przy użyciu programu Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Zmień zawartość $privateConfig i $publicConfig według różnych scenariuszy w poprzedniej sekcji.
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

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kod błędu: 51 reprezentuje nieobsługiconą dystrybucję lub nieobsługiwała akcji rozszerzenia.
W niektórych przypadkach rozszerzenie DSC Linux nie może zainstalować OMI, gdy w komputerze istnieje już wyższa wersja OMI. [odpowiedź na błąd: (000003)Nie dopuszcza się zmiany na starszą wersję?



### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, skontaktuj się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje dotyczące korzystania z pomocy technicznej platformy Azure, przeczytaj [często zadawane pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Linux](features-linux.md).
