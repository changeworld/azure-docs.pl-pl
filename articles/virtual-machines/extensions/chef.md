---
title: Rozszerzenie programu Chef dla maszyn wirtualnych platformy Azure
description: Wdrażanie klienta szefa kuchni na maszynie wirtualnej przy użyciu rozszerzenia maszyny wirtualnej szefa kuchni.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: a21b8f2fea7433e9f65fd790321a28ea47a38c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76544722"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Rozszerzenie maszyny wirtualnej chef dla systemów Linux i Windows

Firma Chef Software udostępnia platformę automatyzacji w metodyce DevOps dla systemów Linux i Windows, która umożliwia zarządzanie konfiguracjami zarówno serwerów fizycznych, jak i wirtualnych. Rozszerzenie maszyny wirtualnej chef jest rozszerzeniem, które umożliwia chef na maszynach wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej szefa kuchni jest obsługiwane na wszystkich [obsługiwanych rozszerzeniach systemu operacyjnego](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) na platformie Azure.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie maszyny Wirtualnej chef wymaga, aby docelowa maszyna wirtualna była połączona z Internetem w celu pobrania ładunku klienta szefa kuchni z sieci dostarczania zawartości (CDN).  

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia maszyny wirtualnej szefa kuchni. Rozszerzenie wymaga co najmniej adresu URL serwera szefa kuchni, nazwy klienta sprawdzania poprawności i klucza sprawdzania poprawności dla serwera szefa kuchni; wartości te można znaleźć `knife.rb` w pliku w starter-kit.zip, który jest pobierany po zainstalowaniu [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) lub samodzielny [Chef Server](https://downloads.chef.io/chef-server). Ponieważ klucz sprawdzania poprawności powinny być traktowane jako poufne dane, powinny być skonfigurowane w ramach **protectedSettings** element, co oznacza, że będzie odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Podstawowe wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych
| ---- | ---- | ----
| apiVersion | `2017-12-01` | ciąg (data) |
| wydawca | `Chef.Bootstrap.WindowsAzure` | ciąg |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | ciąg |
| typHandlerVersion | `1210.13` | ciąg (podwójny) |

### <a name="settings"></a>Ustawienia

| Nazwa | Wartość / Przykład | Typ danych | Wymagana?
| ---- | ---- | ---- | ----
| ustawienia/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | ciąg (adres URL) | Tak |
| ustawienia/bootstrap_options/validation_client_name | `myorg-validator` | ciąg | Tak |
| ustawienia/lista uruchomień | `recipe[mycookbook::default]` | ciąg | Tak |

### <a name="protected-settings"></a>Ustawienia chronione

| Nazwa | Przykład | Typ danych | Wymagana?
| ---- | ---- | ---- | ---- |
| chronioneStawy/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | ciąg | Tak |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Szablony mogą być używane do wdrażania jednej lub więcej maszyn wirtualnych, instalowania klienta szefa kuchni, łączenia się z serwerem szefa kuchni i wykonywania początkowej konfiguracji na serwerze zgodnie z [definicją na liście Uruchamiania](https://docs.chef.io/run_lists.html)

Przykładowy szablon Menedżera zasobów zawierający rozszerzenie maszyny wirtualnej szefa kuchni można znaleźć w [galerii szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Konfiguracja JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżona wewnątrz zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia maszyny Wirtualnej szefa kuchni do istniejącej maszyny Wirtualnej. Zastąp **validation_key** zawartością klucza sprawdzania poprawności (ten plik jako `.pem` rozszerzenie).  Zastąp **validation_client_name,** **chef_server_url** i **run_list** tymi wartościami z `knife.rb` pliku w zestawie startowym.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenia

| Kod błędu | Znaczenie | Możliwe działanie |
| :---: | --- | --- |
| 51 | To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny Wirtualnej | |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w [readme Rozszerzenia maszyny Wirtualnej szefa kuchni](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> Aby uzyskać informacje o czymś innym bezpośrednio związanym z szefem kuchni, skontaktuj się z [pomocą techniczną szefa kuchni.](https://www.chef.io/support/)

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
