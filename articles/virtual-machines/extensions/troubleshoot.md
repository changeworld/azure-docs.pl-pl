---
title: Rozwiązywanie problemów z błędami rozszerzenia maszyny wirtualnej systemu Windows | Microsoft Docs
description: Dowiedz się więcej na temat rozwiązywania problemów z błędami rozszerzenia maszyny wirtualnej
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
ms.openlocfilehash: 6108b3c8d4f4c5c93bc2543042294bc8167d4dd9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084287"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Rozwiązywanie problemów z błędami rozszerzenia maszyny wirtualnej systemu Windows Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Wyświetlanie stanu rozszerzenia
Szablony Azure Resource Manager mogą być wykonywane z Azure PowerShell. Po wykonaniu szablonu stan rozszerzenia może być wyświetlany z poziomu Azure Resource Explorer lub narzędzi wiersza polecenia.

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
W przypadku uruchamiania skryptów na maszynie wirtualnej przy użyciu rozszerzenia niestandardowego skryptu można czasami uruchomić polecenie w przypadku, gdy maszyna wirtualna została utworzona pomyślnie, ale skrypt nie powiódł się. W tych warunkach zalecany sposób odzyskania po tym błędzie polega na usunięciu rozszerzenia i ponownym uruchomieniu szablonu.
Uwaga: W przyszłości ta funkcja zostanie rozszerzona w celu usunięcia konieczności odinstalowania rozszerzenia.

#### <a name="remove-the-extension-from-azure-powershell"></a>Usuń rozszerzenie z Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Po usunięciu rozszerzenia można ponownie wykonać szablon w celu uruchomienia skryptów na maszynie wirtualnej.

