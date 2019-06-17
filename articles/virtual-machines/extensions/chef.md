---
title: Rozszerzenie programu chef dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Wdrażanie klienta programu Chef do maszyny wirtualnej przy użyciu programu Chef rozszerzenia maszyny Wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 6bd3ea4e664523fe8014be40c51d573ed5158ecf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60800266"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Program chef rozszerzenia maszyny Wirtualnej dla systemów Linux i Windows

Firma Chef Software udostępnia platformę automatyzacji w metodyce DevOps dla systemów Linux i Windows, która umożliwia zarządzanie konfiguracjami zarówno serwerów fizycznych, jak i wirtualnych. Rozszerzenie maszyny Wirtualnej programu Chef to rozszerzenie, które umożliwia Chef na maszynach wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Program Chef rozszerzenia maszyny Wirtualnej jest obsługiwana we wszystkich [rozszerzenia obsługiwane systemu operacyjnego](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) na platformie Azure.

### <a name="internet-connectivity"></a>Łączność z Internetem

Program Chef rozszerzenia maszyny Wirtualnej wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem w celu pobrania ładunku klienta Chef z sieci dostarczania zawartości (CDN).  

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia maszyny Wirtualnej programu Chef. Rozszerzenie wymaga co najmniej adres URL serwera programu Chef, nazwę klienta do weryfikacji i klucz sprawdzania poprawności dla serwera programu Chef; te wartości można znaleźć w `knife.rb` plik startowy — kit.zip, który jest pobierany podczas instalowania [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) lub autonomiczny [serwera Chef](https://downloads.chef.io/chef-server). Ponieważ klucz sprawdzania poprawności powinny być traktowane jako poufne dane, należy skonfigurować w obszarze **protectedSettings** elementu, co oznacza, że tylko zostanie on odszyfrowany docelowej maszyny wirtualnej.

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

### <a name="core-property-values"></a>Wartości właściwości podstawowe

| Name (Nazwa) | Wartość / przykład | Typ danych
| ---- | ---- | ---- 
| apiVersion | `2017-12-01` | ciąg (Data) |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.12` | ciąg (podwójny) |

### <a name="settings"></a>Ustawienia

| Name (Nazwa) | Wartość / przykład | Typ danych | Wymagana?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | ciąg (url) | Tak |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Tak |
| settings/runlist | `recipe[mycookbook::default]` | string | Tak |

### <a name="protected-settings"></a>Chronione ustawienia

| Name (Nazwa) | Przykład | Typ danych | Wymagana?
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

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony mogą być używane do wdrożenia co najmniej jednej maszyny wirtualnej, zainstalować klienta programu Chef, połączyć się z serwerem Chef i wykonaj początkową konfigurację na serwerze zgodnie z definicją [wykonywania listy](https://docs.chef.io/run_lists.html)

Przykładowy szablon usługi Resource Manager, obejmującą Chef rozszerzenia maszyny Wirtualnej można znaleźć na [w galerii platformy Azure Szybki Start](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do wdrożenia rozszerzenia maszyny Wirtualnej programu Chef do istniejącej maszyny Wirtualnej. Zastąp **validation_key** z zawartością klucz sprawdzania poprawności (ten plik jako `.pem` rozszerzenia).  Zastąp **validation_client_name**, **chef_server_url** i **run_list** tymi wartościami z `knife.rb` pliku w swojej Starter Kit.

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
| 51 | To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny Wirtualnej | |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w [readme rozszerzenia maszyny Wirtualnej programu Chef](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
