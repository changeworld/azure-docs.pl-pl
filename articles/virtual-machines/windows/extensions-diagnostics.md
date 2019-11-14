---
title: Diagnostyka Azure rozszerzenie dla systemu Windows
description: Monitorowanie maszyn wirtualnych z systemem Windows Azure przy użyciu rozszerzenia Diagnostyka Azure
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 3fcdcd4565ed0512b3efcf9b23ca349a3bbf3a6c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033475"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Diagnostyka Azure rozszerzenie dla maszyn wirtualnych z systemem Windows

## <a name="overview"></a>Omówienie

Rozszerzenie maszyny wirtualnej Diagnostyka Azure umożliwia zbieranie danych monitorowania, takich jak liczniki wydajności i dzienniki zdarzeń, z maszyny wirtualnej z systemem Windows. Można szczegółowo określić, jakie dane mają być zbierane, oraz miejsce, w którym mają być wykonywane dane, takie jak konto usługi Azure Storage lub centrum zdarzeń platformy Azure. Możesz również użyć tych danych do kompilowania wykresów w Azure Portal lub tworzenia alertów dotyczących metryk.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Diagnostyka Azure można uruchomić dla klienta systemu Windows 10, Windows Server 2008 R2, 2012, 2012 R2 i 2016.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Diagnostyka Azure wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

[W tym dokumencie opisano Diagnostyka Azure schematu i wartości właściwości rozszerzenia.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie Diagnostyka Azure podczas wdrażania szablonu Azure Resource Manager. Zobacz [Używanie monitorowania i diagnostyki z maszynami wirtualnymi z systemem Windows i szablonami Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia Diagnostyka Azure na istniejącej maszynie wirtualnej. Zastąp właściwości ustawienia i ustawienia chronione prawidłowym kodem JSON z powyższego schematu rozszerzenia. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Wdrażanie programu PowerShell

Za pomocą polecenia `Set-AzVMDiagnosticsExtension` można dodać rozszerzenie Diagnostyka Azure do istniejącej maszyny wirtualnej. [Aby włączyć Diagnostyka Azure w maszynie wirtualnej z systemem Windows, zobacz również korzystanie z programu PowerShell](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Zapoznaj się z [tym artykułem](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) , aby uzyskać bardziej szczegółowy przewodnik rozwiązywania problemów dla rozszerzenia Diagnostyka Azure.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o rozszerzeniu Diagnostyka Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Przejrzyj schemat i wersje rozszerzeń](../../azure-monitor/platform/diagnostics-extension-schema.md)
