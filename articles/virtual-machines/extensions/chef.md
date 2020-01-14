---
title: Rozszerzenie Chef dla maszyn wirtualnych platformy Azure
description: Wdróż klienta Chef na maszynie wirtualnej przy użyciu rozszerzenia maszyny wirtualnej Chef.
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
ms.openlocfilehash: 8a5b54131210d243015b37bf234408fd9d2b4c12
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933623"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Rozszerzenie maszyny wirtualnej Chef dla systemów Linux i Windows

Firma Chef Software udostępnia platformę automatyzacji w metodyce DevOps dla systemów Linux i Windows, która umożliwia zarządzanie konfiguracjami zarówno serwerów fizycznych, jak i wirtualnych. Rozszerzenie maszyny wirtualnej Chef jest rozszerzeniem umożliwiającym Chef na maszynach wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej Chef jest obsługiwane na wszystkich [obsługiwanych systemach operacyjnych](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) na platformie Azure.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie maszyny wirtualnej Chef wymaga, aby docelowa maszyna wirtualna była połączona z Internetem w celu pobrania ładunku klienta Chef z usługi Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Chef. Rozszerzenie wymaga co najmniej adresu URL serwera Chef, nazwy klienta weryfikacji i klucza weryfikacji dla serwera Chef; te wartości można znaleźć w pliku `knife.rb` Starter-Kit. zip, który jest pobierany podczas instalowania [Chef automatyzuje](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) lub autonomicznego [serwera Chef](https://downloads.chef.io/chef-server). Ponieważ klucz weryfikacji powinien być traktowany jako dane poufne, powinien być skonfigurowany w ramach elementu **protectedSettings** , co oznacza, że zostanie on odszyfrowany tylko na docelowej maszynie wirtualnej.

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
    "typeHandlerVersion": "1210.12",
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

| Nazwa | Wartość / przykład | Typ danych
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux), `ChefClient` (system Windows) | string |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>Ustawienia

| Nazwa | Wartość / przykład | Typ danych | Wymagana?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | Tak |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Tak |
| Ustawienia/runlist | `recipe[mycookbook::default]` | string | Tak |

### <a name="protected-settings"></a>Ustawienia chronione

| Nazwa | Przykład | Typ danych | Wymagana?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Tak |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Za pomocą szablonów można wdrożyć co najmniej jedną maszynę wirtualną, zainstalować klienta Chef, nawiązać połączenie z serwerem Chef i wykonać konfigurację początkową na serwerze, zgodnie z definicją z [listy uruchamiania](https://docs.chef.io/run_lists.html)

Przykładowy szablon Menedżer zasobów, który zawiera rozszerzenie maszyny wirtualnej Chef, można znaleźć w [galerii szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do wdrożenia rozszerzenia maszyny wirtualnej Chef na istniejącej maszynie wirtualnej. Zastąp **validation_key** zawartością klucza weryfikacji (ten plik jako rozszerzenie `.pem`).  Zastąp **validation_client_name**, **chef_server_url** i **run_list** tymi wartościami z pliku `knife.rb` w twoim zestawie startowym.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 51 | To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny wirtualnej. | |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w [pliku Readme rozszerzenia maszyny wirtualnej Chef](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> W przypadku wszystkich innych elementów bezpośrednio związanych z Chef skontaktuj się z [pomocą techniczną Chef](https://www.chef.io/support/).

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
