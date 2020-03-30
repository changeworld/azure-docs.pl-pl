---
title: Rozwiązywanie problemów z błędami rozszerzenia maszyny Wirtualnej systemu Windows
description: Dowiedz się więcej o rozwiązywaniu problemów z błędami rozszerzenia maszyny Wirtualnej systemu Windows platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bc99a9c9e9ff985730ec97dbacd1d7c1de06a45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073650"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Rozwiązywanie problemów z błędami rozszerzenia maszyny Wirtualnej systemu Windows platformy Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Wyświetlanie stanu rozszerzenia
Szablony usługi Azure Resource Manager można wykonać z usługi Azure PowerShell. Po wykonaniu szablonu stan rozszerzenia można wyświetlić za pomocą Eksploratora zasobów platformy Azure lub narzędzi wiersza polecenia.

Oto przykład:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Oto przykładowe dane wyjściowe:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Rozwiązywanie problemów z błędami rozszerzeń
### <a name="rerun-the-extension-on-the-vm"></a>Uruchom ponownie rozszerzenie na maszynie wirtualnej
Jeśli używasz skryptów na maszynie wirtualnej przy użyciu niestandardowego rozszerzenia skryptu, czasami można napotkać błąd, w którym maszyna wirtualna została utworzona pomyślnie, ale skrypt nie powiódł się. W tych warunkach zalecanym sposobem odzyskania danych z tego błędu jest usunięcie rozszerzenia i ponowne uruchomienie szablonu.
Uwaga: W przyszłości ta funkcja zostanie ulepszona, aby usunąć konieczność odinstalowania rozszerzenia.

#### <a name="remove-the-extension-from-azure-powershell"></a>Usuwanie rozszerzenia z programu Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Po usunięciu rozszerzenia szablon może zostać ponownie wykonany w celu uruchomienia skryptów na maszynie Wirtualnej.

