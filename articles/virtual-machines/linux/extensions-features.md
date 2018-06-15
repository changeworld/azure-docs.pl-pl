---
title: Rozszerzenia maszyn wirtualnych i funkcji w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według co ich Podaj puli zasobów i zwiększyć.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 47cc812f9dac606cf4f69df9eff6d48095eb6ef8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32191179"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozszerzenia maszyn wirtualnych i funkcji w systemie Linux

Rozszerzenia maszyny wirtualnej platformy Azure są małe aplikacji, które mają po wdrożeniu i automatyzację zadań na maszynach wirtualnych Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony oprogramowania antywirusowego lub Docker konfiguracji, rozszerzenia maszyny Wirtualnej może służyć do wykonania tych zadań. Rozszerzenia maszyny Wirtualnej platformy Azure mogą być uruchamiane przy użyciu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i portalu Azure. Rozszerzenia mogą być powiązane z nowego wdrożenia maszyny wirtualnej lub uruchomienia dowolnego istniejącego systemu.

Ten dokument zawiera omówienie rozszerzeń maszyny Wirtualnej, wymagań wstępnych dotyczących używania rozszerzenia maszyny Wirtualnej platformy Azure i instrukcje dotyczące wykrywania, zarządzanie i usuwanie rozszerzenia maszyny Wirtualnej. Ten dokument zawiera informacje uogólniony wielu rozszerzeń maszyny Wirtualnej nie są dostępne, każde z nich potencjalnie unikatowych konfiguracji. Szczegółów dotyczących poszczególnych rozszerzeń można znaleźć w każdy dokument specyficzne dla poszczególnych rozszerzenia.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Dostępnych jest kilka różnych rozszerzeń maszyny Wirtualnej platformy Azure, każde z nich określony przypadek użycia. Przykłady to:

- Zastosuj PowerShell żądany stan konfiguracje dla maszyny wirtualnej dla systemu Linux przy użyciu rozszerzenia usługi Konfiguracja DSC. Aby uzyskać więcej informacji, zobacz [Azure żądany stan konfiguracji rozszerzenia](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurowanie monitorowania maszyny wirtualnej z rozszerzenia maszyny Wirtualnej agenta monitorowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [jak monitorować Maszynę wirtualną systemu Linux](tutorial-monitoring.md).
- Skonfiguruj Monitorowanie infrastruktury platformy Azure z rozszerzeniem Datadog. Aby uzyskać więcej informacji, zobacz [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Konfigurowanie hosta Docker na maszynie wirtualnej platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker. Aby uzyskać więcej informacji, zobacz [rozszerzenia maszyny Wirtualnej platformy Docker](dockerextension.md).

Oprócz rozszerzenia procesu rozszerzenia niestandardowego skryptu jest dostępna dla maszyn wirtualnych w systemach Windows i Linux. Rozszerzenia niestandardowego skryptu dla systemu Linux umożliwia dowolny skrypt Bash do uruchamiania na maszynie wirtualnej. Niestandardowe skrypty są przydatne w przypadku projektowania wdrożeń platformy Azure, które wymagają konfiguracji oprócz zapewniają jakie natywnego narzędzia Azure. Aby uzyskać więcej informacji, zobacz [rozszerzenie skryptu niestandardowego maszyny Wirtualnej systemu Linux](extensions-customscript.md).


## <a name="prerequisites"></a>Wymagania wstępne

Każde rozszerzenie maszyny wirtualnej może mieć własny zestaw wymagań wstępnych. Dla wystąpienia rozszerzenia maszyny Wirtualnej platformy Docker jest wymaganiem wstępnym obsługiwanych dystrybucji systemu Linux. Wymagania dotyczące poszczególnych rozszerzeń są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny Wirtualnej platformy Azure zarządza interakcje między maszyny wirtualnej platformy Azure i kontroler sieci szkieletowej Azure. Agent maszyny Wirtualnej jest odpowiedzialny za dużo aspekty funkcjonalne, wdrażania i zarządzania maszynami wirtualnymi platformy Azure, łącznie z rozszerzeniami maszyny Wirtualnej. Agent maszyny Wirtualnej platformy Azure jest preinstalowany na portalu Azure Marketplace obrazów i można zainstalować ręcznie w obsługiwanych systemach operacyjnych.

Aby uzyskać informacje dotyczące obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [agenta maszyny wirtualnej platformy Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Odnajdywanie rozszerzeń maszyny Wirtualnej

Wiele różnych rozszerzeń maszyny Wirtualnej są dostępne do użycia z maszyn wirtualnych platformy Azure. Aby wyświetlić pełną listę, uruchom następujące polecenie z wiersza polecenia platformy Azure, zastępując lokalizacji przykład wybranej lokalizacji.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Uruchom rozszerzeń maszyny Wirtualnej

Rozszerzenia maszyny wirtualnej platformy Azure może działać w istniejących maszyn wirtualnych, które są przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub Przywróć łączność w przypadku wdrożonej maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej dołączany także wdrażanie szablonów usługi Azure Resource Manager. Za pomocą rozszerzeń z szablonami usługi Resource Manager, można wdrożyć i konfigurować bez interwencji po wdrożeniu maszyn wirtualnych platformy Azure.

Następujących metod można uruchomić rozszerzenia dla istniejącej maszyny wirtualnej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Rozszerzenia maszyny wirtualnej platformy Azure mogą być uruchamiane na istniejącej maszyny wirtualnej za pomocą `az vm extension set` polecenia. W tym przykładzie jest uruchamiana rozszerzenie skryptu niestandardowego dla maszyny wirtualnej.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Skrypt generuje dane wyjściowe podobne do następującego tekstu:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyny Wirtualnej może odnosić się do istniejącej maszyny wirtualnej za pośrednictwem portalu Azure. Aby to zrobić, wybierz maszynę wirtualną, wybierz **rozszerzenia**i kliknij przycisk **Dodaj**. Wybierz rozszerzenia, z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami w kreatorze.

Na poniższej ilustracji przedstawiono instalacja rozszerzenia Linux niestandardowego skryptu z portalu Azure.

![Zainstaluj rozszerzenie skryptu niestandardowego](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny Wirtualnej można dodać do szablonu usługi Azure Resource Manager i wykonywane przy użyciu wdrażania szablonu. Podczas wdrażania rozszerzenia za pomocą szablonu można tworzyć wdrożeń platformy Azure w pełni skonfigurowany. Na przykład następujący kod JSON jest pobierana z szablonem usługi Resource Manager. Szablon wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i bazy danych Azure SQL, a następnie instaluje aplikację .NET Core na każdej maszynie Wirtualnej. Rozszerzenia maszyny Wirtualnej zapewnia obsługę instalacji oprogramowania.

Aby uzyskać więcej informacji, zobacz pełny [szablonu usługi Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Aby uzyskać więcej informacji, zobacz [szablonów Authoring Azure Resource Manager](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny Wirtualnej

Jeśli korzystasz z rozszerzenia maszyny Wirtualnej, może być konieczne jest stosowanie poufne informacje, takie jak poświadczeń, nazwy konta magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyny Wirtualnej obejmują konfiguracji chronionych danych i tylko odszyfrowującego wewnątrz docelowej maszyny wirtualnej. Każde rozszerzenie ma schematu określonej konfiguracji chronionych, a każdy szczegółowo opisano w dokumentacji konkretnego rozszerzenia.

W poniższym przykładzie przedstawiono wystąpienia rozszerzenia niestandardowego skryptu dla systemu Linux. Zwróć uwagę, że polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie wykonanie polecenia nie będą szyfrowane.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Przenoszenie **polecenie do wykonania** właściwości **chronione** konfiguracja zabezpiecza ciąg wykonywania.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeń maszyny Wirtualnej

Każde rozszerzenie maszyny Wirtualnej może być rozwiązywania problemów z określonego rozszerzenia. Na przykład jeśli używasz rozszerzenia niestandardowego skryptu, szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie wirtualnej, na którym uruchomiono rozszerzenia. Wszystkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

Następujące kroki dotyczą wszystkich rozszerzeń maszyny wirtualnej.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po maszyny wirtualnej była uruchamiana rozszerzenia maszyny wirtualnej, użyj następującego polecenia wiersza polecenia platformy Azure ma zostać zwrócony stan rozszerzenia. Zastąp przykładowe nazwy parametrów własne wartości.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wyglądają następująco następujący tekst:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Stan wykonania rozszerzenia można znaleźć w portalu Azure. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz polecenie **rozszerzenia**i wybierz żądane rozszerzenie.

### <a name="rerun-a-vm-extension"></a>Uruchom ponownie rozszerzenia maszyny Wirtualnej

Może to być przypadkach, w których można ponownie uruchomić wymaga rozszerzenia maszyny wirtualnej. Możesz ponownie uruchomić rozszerzenia, usuwając go i następnie ponowne uruchomienie rozszerzenia metodą wykonywania wybranych przez użytkownika. Aby usunąć rozszerzenie, uruchom następujące polecenie z wiersza polecenia platformy Azure. Zastąp przykładowe nazwy parametrów własne wartości.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Aby usunąć rozszerzenie, należy w następujących krokach w portalu Azure:

1. Wybierz maszynę wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **odinstalować**.

## <a name="common-vm-extension-reference"></a>Typowe odwołania do rozszerzenia maszyny Wirtualnej
| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Niestandardowe rozszerzenie skryptu dla systemu Linux |Uruchom skrypty przed maszyny wirtualnej platformy Azure |[Niestandardowe rozszerzenie skryptu dla systemu Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Rozszerzenie docker |Instalowanie demona Docker do obsługi poleceń zdalnych Docker. |[Rozszerzenie maszyny wirtualnej Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Rozszerzenie dostępu do maszyny wirtualnej |Odzyskać dostęp do maszyny wirtualnej platformy Azure |[Rozszerzenie dostępu do maszyny wirtualnej](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozszerzenie Diagnostyka Azure |Zarządzanie Diagnostyka Azure |[Rozszerzenie Diagnostyka Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenia dostępu do maszyny Wirtualnej platformy Azure |Zarządzaj użytkownikami i poświadczenia |[Rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
