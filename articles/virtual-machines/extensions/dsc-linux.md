---
title: Rozszerzenie DSC platformy Azure dla systemu Linux
description: Instaluje pakiety OMI i DSC, aby zezwolić na maszynie Wirtualnej systemu Linux platformy Azure do skonfigurowania za pomocą Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 67f72c5b396bc935f7bec34bc8a52f63131649b1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904478"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozszerzenie DSC dla systemu Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) to platforma zarządzania, która pozwala na zarządzanie IT i rozwój infrastruktury za pomocą konfiguracji jako kodu.

Rozszerzenie DSCForLinux jest opublikowany i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta programu OMI i DSC na maszynach wirtualnych platformy Azure. Rozszerzenie DSC można również wykonać następujące czynności


- Zarejestruj maszynę Wirtualną systemu Linux do konta usługi Azure Automation w celu ściągania konfiguracji z usługi Azure Automation (Zarejestruj ExtensionAction)
- Wypychanie pliku MOF konfiguracji do maszyny Wirtualnej systemu Linux (Push ExtensionAction)
- Zastosuj konfigurację Meta pliku MOF do maszyny Wirtualnej systemu Linux, aby skonfigurować serwer ściągania w celu ściągania konfiguracji węzła (ściągnięcia ExtensionAction)
- Zainstaluj moduły niestandardowe DSC maszyny Wirtualnej systemu Linux (instalowanie ExtensionAction)
- Usuń niestandardowe moduły DSC do maszyny Wirtualnej systemu Linux (Usuń ExtensionAction)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie DSC w systemie Linux obsługuje wszystkie [dystrybucje systemu Linux zalecanych dla na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) z wyjątkiem:

| Dystrybucja | Wersja |
|---|---|
| Debian | Wszystkie wersje |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie DSCForLinux wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem. Na przykład rozszerzenie rejestru wymaga połączenia z usługą Automation. Inne akcje, takie jak ściągania ściągania, instalacja wymaga łączności z magazynu platformy azure/github. To zależy od ustawienia udostępnione przez klienta.

## <a name="extension-schema"></a>Schemat rozszerzenia

### <a name="11-public-configuration"></a>1.1 Konfiguracja publiczna

W tym miejscu znajdziesz wszystkie parametry obsługiwanej konfiguracji publicznego:

* `FileUri`: (opcjonalnie, ciąg) identyfikator uri pliku ZIP zasobów pliku/niestandardowa MOF pliku/Meta pliku MOF.
* `ResourceName`: (opcjonalnie, ciąg) Nazwa modułu zasobów niestandardowych
* `ExtensionAction`: (opcjonalnie, ciąg) określa, jak działa rozszerzenie. Prawidłowe wartości: Rejestrowanie, wypychania, ściągania, zainstalować, usunąć. Jeśli nie zostanie określony, uznaje się jako akcja Push domyślnie.
* `NodeConfigurationName`: (opcjonalnie, ciąg) Nazwa konfiguracji węzła do zastosowania.
* `RefreshFrequencyMins`: (opcjonalne, int) określa, jak często (w minutach), próbuje uzyskać konfigurację z serwera ściągania DSC. 
       Jeśli konfiguracji na serwerze ściągania różni się od bieżącej nazwy na węzeł docelowy, jest kopiowany do magazynu oczekujące i stosowane.
* `ConfigurationMode`: (opcjonalnie, ciąg) określa sposób stosowania konfiguracji DSC. Prawidłowe wartości to: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcjonalne, int) określa, jak często (w minutach), DSC zapewnia, że konfiguracja jest w żądanym stanie.

> [!NOTE]
> Jeśli używasz wersji < 2.3 parametrem mode jest taka sama jak ExtensionAction. Tryb wydaje się być przeciążona termin. W związku z tym aby uniknąć nieporozumień, ExtensionAction jest używana z w wersji 2.3 lub nowszy. W celu zapewnienia zgodności z poprzednimi wersjami rozszerzenie obsługuje zarówno w trybie, jak i ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1.2 chronionych konfiguracji

W tym miejscu znajdziesz wszystkie parametry obsługiwanej konfiguracji chronionych:

* `StorageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu, który zawiera plik
* `StorageAccountKey`: (opcjonalnie, ciąg) klucz konta magazynu, który zawiera plik
* `RegistrationUrl`: (opcjonalnie, ciąg) adres URL konta usługi Azure Automation
* `RegistrationKey`: (opcjonalnie, ciąg) klucz dostępu konta usługi Azure Automation


## <a name="scenarios"></a>Scenariusze

### <a name="register-to-azure-automation-account"></a>Zarejestruj się, aby konto usługi Azure Automation
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Zastosowanie pliku MOF konfiguracji (na koncie magazynu platformy Azure) do maszyny Wirtualnej

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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Zastosowanie pliku MOF konfiguracji (w publicznym magazynie) do maszyny Wirtualnej

public.json
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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Zastosowanie pliku metadanych pliku MOF konfiguracji (na koncie magazynu platformy Azure) do maszyny Wirtualnej

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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Zastosowanie pliku metadanych pliku MOF konfiguracji (w publicznym magazynie) do maszyny Wirtualnej
public.json
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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Zainstaluj moduł zasobów niestandardowych (plik ZIP na koncie magazynu platformy Azure) do maszyny Wirtualnej
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Zainstaluj moduł zasobów niestandardowych (plik ZIP w publicznym magazynie) do maszyny Wirtualnej
public.json
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Usuń zasób niestandardowy z maszyny Wirtualnej
public.json
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

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealnym rozwiązaniem w przypadku wdrażania maszyn wirtualnych, które wymagają konfiguracji po wdrożeniu, takich jak dołączanie do usługi Azure Automation. 

Przykładowy szablon usługi Resource Manager jest [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) i [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Aby uzyskać więcej informacji na temat szablonu usługi Azure Resource Manager, odwiedź stronę [tworzenia usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

### <a name="21-using-azure-cliazure-cli"></a>2.1. Przy użyciu [**wiersza polecenia platformy Azure**] [azure-cli]
Przed wdrożeniem DSCForLinux rozszerzenie, należy skonfigurować usługi `public.json` i `protected.json`, zgodnie z różnych scenariuszy, w sekcji 3.

#### <a name="211-classic"></a>2.1.1. Wdrożenie klasyczne
Trybu klasycznego jest również nazywany trybie Azure Service Management. Możesz przełączyć się do niego, uruchamiając:
```
$ azure config mode asm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Aby dowiedzieć się, najnowsza dostępna wersja rozszerzenia, uruchom polecenie:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Można przełączyć do trybu usługi Resource Manager platformy Azure, uruchamiając:
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
> W trybie usługi Azure Resource Manager `azure vm extension list` nie jest dostępna teraz.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Przy użyciu [**programu Azure PowerShell**] [azure-powershell]

#### <a name="221-classic"></a>2.2.1 klasyczne

Możesz zalogować się do konta platformy Azure (w trybie Azure Service Management), uruchamiając:

```powershell>
Add-AzureAccount
```

I wdrażanie rozszerzenia DSCForLinux, uruchamiając:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Należy zmienić treść $privateConfig i $publicConfig zgodnie z różnych scenariuszy w powyżej sekcji 
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

#### <a name="222resource-manager"></a>2.2.2.Resource Manager

Możesz zalogować się do konta platformy Azure (tryb usługi Azure Resource Manager), uruchamiając:

```powershell>
Login-AzAccount
```

Kliknij przycisk [ **tutaj** ](../../azure-resource-manager/manage-resources-powershell.md) Aby dowiedzieć się więcej o sposobie używania programu Azure PowerShell z usługą Azure Resource Manager.

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Należy zmienić treść $privateConfig i $publicConfig zgodnie z różnych scenariuszy w powyżej sekcji 
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

Kod błędu: 51 reprezentuje nieobsługiwany dostępnych dla określonych dystrybucji lub nieobsługiwane rozszerzenie akcji.
W niektórych przypadkach Linux DSC, rozszerzenie nie powiedzie się zainstalowanie OMI wyższą wersję programu OMI jest już istnieje na maszynie. [błąd odpowiedzi: (000003) Obniżenie poziomu nie jest dozwolone]



### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux](features-linux.md).
