---
title: Rozszerzenia diagnostyki Azure dla systemu Windows | Dokumentacja firmy Microsoft
description: Monitorowanie maszyn wirtualnych systemu Windows Azure przy użyciu rozszerzenia systemu Windows Azure Diagnostics
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
ms.openlocfilehash: a9621f6f93d8e14e53cfe05ca4a714e88b9d8572
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="windows-azure-diagnostics-extension"></a>Rozszerzenie systemu Windows Azure Diagnostics

## <a name="overview"></a>Przegląd

Rozszerzenie maszyny Wirtualnej systemu Windows Azure Diagnostics umożliwia zbieranie danych monitorowania, takie jak liczniki wydajności i dzienniki zdarzeń z maszyny Wirtualnej systemu Windows. Częściami można określić, jakie dane mają być zbierane i miejscu danych, aby przejść, takich jak konta magazynu Azure lub usługi Azure Event Hub. Te dane umożliwia również tworzenie wykresów w portalu Azure lub tworzyć alerty metryki.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Diagnostyka systemu Windows Azure może działać względem klient 10 systemu Windows, Windows Server 2008 R2, 2012 i 2012 R2, 2016.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Diagnostyka systemu Windows Azure wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem. 

## <a name="extension-schema"></a>Rozszerzenie schematu

[Windows Azure Diagnostics rozszerzenia schematu i wartości właściwości są opisane w tym dokumencie.](../../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md)

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager rozszerzenie systemu Windows Azure Diagnostics są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. Zobacz [Użyj monitorowania i diagnostyki z szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI

Interfejsu wiersza polecenia Azure może służyć do wdrożenia systemu Windows Azure Diagnostics rozszerzenia istniejącej maszyny wirtualnej. Zastąp ustawienia chronionych i właściwości ustawień poprawne dane JSON z powyższego schematu rozszerzenia. 

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

`Set-AzureRmVMDiagnosticsExtension` Polecenia można dodać rozszerzenie systemu Windows Azure Diagnostics istniejącej maszyny wirtualnej. Zobacz też [Użyj programu PowerShell, aby włączyć na maszynie wirtualnej z systemem Windows Azure Diagnostics](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Znajduje się w artykule](../../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) więcej kompletny przewodnik rozwiązywania problemów dla rozszerzenia diagnostyki Windows Azure.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o rozszerzeniu diagnostyki Windows Azure](../../monitoring-and-diagnostics/azure-diagnostics.md)
* [Przejrzyj rozszerzenia schematu i wersje](../../monitoring-and-diagnostics/azure-diagnostics-schema.md)
