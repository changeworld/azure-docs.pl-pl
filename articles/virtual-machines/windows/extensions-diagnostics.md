---
title: Rozszerzenie diagnostyki platformy Azure dla Windows | Dokumentacja firmy Microsoft
description: Monitorowanie maszyn wirtualnych Windows Azure przy użyciu rozszerzenia diagnostyki Azure
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
ms.openlocfilehash: 58c520ecbaf764140748167e458c301ab56de375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708064"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Rozszerzenie diagnostyki platformy Azure dla maszyn wirtualnych Windows

## <a name="overview"></a>Omówienie

Rozszerzenie maszyny Wirtualnej diagnostyki platformy Azure umożliwia zbieranie danych monitorowania, takich jak liczniki wydajności i dzienników zdarzeń, z maszyną Wirtualną Windows. Precyzyjne można określić, jakie dane mają być zbierane i gdzie mają dane przejść, takich jak konto usługi Azure Storage lub usługi Azure Event Hub. Te dane umożliwia również tworzenie wykresów w witrynie Azure portal lub tworzyć alerty dotyczące metryk.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie diagnostyki platformy Azure mogą być uruchamiane względem klienta w programie Windows 10, Windows Server 2008 R2, 2012, 2012 R2 i 2016.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie diagnostyki platformy Azure wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

[Rozszerzenie Diagnostyka Azure schemat i wartości właściwości są opisane w tym dokumencie.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchomienia przez rozszerzenie Diagnostyka Azure podczas wdrażania szablonu usługi Azure Resource Manager. Zobacz [Użyj monitorowania i diagnostyki za pomocą szablonów maszyn wirtualnych Windows i usługi Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do wdrożenia rozszerzenia diagnostyki Azure do istniejącej maszyny wirtualnej. Zamień na chronionych ustawień i właściwości ustawień prawidłowym kodem JSON z powyższego schematu rozszerzenia. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

`Set-AzVMDiagnosticsExtension` Polecenia można dodać rozszerzenia diagnostyki Azure do istniejącej maszyny wirtualnej. Zobacz też [Diagnostyka Azure maszyny wirtualnej z systemem Windows w programie PowerShell](ps-extensions-diagnostics.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


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

[Ten artykuł](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) bardziej kompletny przewodnik rozwiązywania problemów dla rozszerzenia diagnostyki platformy Azure.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej na temat rozszerzenia diagnostyki Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Przegląd rozszerzeń schematu i wersje](../../azure-monitor/platform/diagnostics-extension-schema.md)
