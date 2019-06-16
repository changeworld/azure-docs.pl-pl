---
title: Rozwiązywanie problemów z błędami rozszerzeń maszyn wirtualnych Windows | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat rozwiązywania problemów z błędami rozszerzeń maszyn wirtualnych Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107248"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Rozwiązywanie problemów z błędami rozszerzeń maszyn wirtualnych Windows Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Wyświetlanie stanu rozszerzenia
Szablony usługi Azure Resource Manager mogą być wykonywane za pomocą programu Azure PowerShell. Po wykonaniu szablonu z Eksploratora zasobów Azure lub narzędzi wiersza polecenia można wyświetlić stan rozszerzenia.

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
### <a name="rerun-the-extension-on-the-vm"></a>Ponownie uruchom rozszerzenie na maszynie Wirtualnej
Jeśli skrypty są uruchomione na maszynie Wirtualnej za pomocą rozszerzenia niestandardowego skryptu, można czasami napotkasz błąd, gdy maszyna wirtualna została utworzona pomyślnie, ale skryptu nie powiodło się. W tych warunkach zalecane sposobu odzyskania sprawności tego błędu jest usunąć rozszerzenie i ponownie szablon.
Uwaga: W przyszłości można rozszerzyć tę funkcję w celu usunięcia potrzeby odinstalować rozszerzenie.

#### <a name="remove-the-extension-from-azure-powershell"></a>Usuń rozszerzenie z programu Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Po usunięciu rozszerzenia szablon może być wykonany ponownie na uruchamianie skryptów na maszynie Wirtualnej.

