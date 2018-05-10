---
title: Rozszerzenie systemu Windows migawki maszyny Wirtualnej dla usługi Kopia zapasowa Azure | Dokumentacja firmy Microsoft
description: Pobranie aplikacji spójne tworzenie kopii zapasowych maszyny wirtualnej z kopii zapasowej Azure przy użyciu rozszerzenia migawki maszyny Wirtualnej
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
editor: ''
ms.assetid: 57759670-0baa-44db-ae14-8cdc00d3a906
ms.service: backup, virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: trinadhk
ms.openlocfilehash: 8426a2472a28cf287dfe574cb80da56108394ae8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Rozszerzenie systemu Windows migawki maszyny Wirtualnej dla usługi Kopia zapasowa Azure

## <a name="overview"></a>Przegląd

Kopia zapasowa Azure zapewnia obsługę tworzenia kopii zapasowych obciążeń z lokalnymi do chmury i wykonywanie kopii zapasowych zasobów chmury w magazynie usług odzyskiwania. Kopia zapasowa Azure korzysta rozszerzenia migawki maszyny Wirtualnej umożliwiające aplikacji spójne tworzenie kopii zapasowych maszyny wirtualnej platformy Azure, bez potrzeby zamykania maszyny Wirtualnej. Rozszerzenie migawki maszyny Wirtualnej jest opublikowana i obsługiwane przez firmę Microsoft w ramach usługi Kopia zapasowa Azure. Kopia zapasowa Azure zainstaluje rozszerzenia w ramach pierwszej zaplanowanej kopii zapasowej wyzwalanych post włączenie kopii zapasowej. Ten dokument zawiera szczegóły dotyczące obsługiwanych platform, konfiguracji i opcje wdrażania dla rozszerzenia migawki maszyny Wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny
Aby uzyskać listę obsługiwanych systemów operacyjnych, zapoznaj się [systemów operacyjnych obsługiwanych przez usługi Kopia zapasowa Azure](../../backup/backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup)

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie migawki maszyny Wirtualnej wymaga, aby docelowej maszyny wirtualnej jest połączony z Internetem, gdy firma Microsoft wykonaj kopię zapasową maszyny wirtualnej.

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schemat rozszerzenia migawki maszyny Wirtualnej. Rozszerzenie wymaga Identyfikatora zadania — identyfikuje kopii zapasowej zadania, które uruchamiane migawki na Maszynie wirtualnej, identyfikator uri obiektu blob stan — w którym zapisywana jest stan operacji migawki zaplanowana godzina rozpoczęcia migawki, dzienniki obiektu blob identyfikatora uri - gdzie dzienniki odpowiadający migawki zadań są zapisywane, reprezentacja objstr dyski maszyny Wirtualnej i metadanych.  Ponieważ te ustawienia mają być traktowane jako poufne dane, powinny być przechowywane w chronionej konfiguracji. Dane Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest szyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że te ustawienia są zalecane do przekazania z usługi Kopia zapasowa Azure tylko w ramach zadania tworzenia kopii zapasowej.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri represenattion of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | ciąg |
| commandStartTimeUTCTicks | 6.36458E + 17 | ciąg |
| Ustawienia regionalne | en-us | ciąg |
| objectStr | Kodowanie w tablicy identyfikatora uri sygnatury dostępu współdzielonego — "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/wirtualne dyski twarde\/vmwin1404ltsc201652903941.vhd? sv = 2014-02-14 & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14% 3A23% 3A28Z & żyj = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/\/sopattna8461.blob.core.windows.net\/dyskówVHD\/vmwin1404ltsc-20160629-122418.vhd? sv = 2014-02-14 & sr = b & sig = 2BS % 2FqMwzFMbamT5upwx05v8Q 5S0A6YDWvVwqPAkzWXVy % 3D & st = 2017-11-09T14% 3A23% 3A28Z & j = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/ \/ sopattna8461.blob.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? sv = 2014-02-14 & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & st = 2017-11-09T14% 3A23% 3A28Z & żyj = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/\/sopattna5365.blob.core.windows.net\/dyskówVHD\/vmwin1404ltsc-20160701-163922.vhd? sv = 2014-02-14 & sr = b & sig = 2BC % 2BNIAork oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 3D & st = 2017-11-09T14% 3A23% 3A28Z & j = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/ \/ sopattna5365.blob.Core.Windows.NET\/wirtualne dyski twarde\/vmwin1404ltsc-20170705-124311.vhd? sv = 2014-02-14 & sr = b & sig = 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0 ZUM9d28Mvvm % 3D & st = 2017-11-09T14% 3A23% 3A28Z & SE = 2017-11-09T17% 3A38% 3A28Z & sp = rw "] | ciąg |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg |



## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Jednak zalecany sposób dodawania rozszerzenia migawki maszyny Wirtualnej na maszynie wirtualnej jest umożliwiając wykonanie kopii zapasowej na maszynie wirtualnej. Można to osiągnąć za pomocą szablonu usługi Resource Manager.  Przykładowy szablon Menedżera zasobów, który umożliwia wykonanie kopii zapasowej na maszynie wirtualnej można znaleźć w [Azure Szybki Start galerii](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI

Interfejsu wiersza polecenia Azure może służyć do włączenia kopii zapasowej na maszynie wirtualnej. Post włączenie wykonywania kopii zapasowych pierwszego zaplanowanego zadania tworzenia kopii zapasowej zainstaluje rozszerzenia migawki maszyny wirtualnej na maszynie Wirtualnej.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenie jest zarejestrowane w następującym pliku:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

Informacje dotyczące rozwiązywania problemów można znaleźć w [kopii zapasowej maszyny Wirtualnej Azure, przewodnik rozwiązywania problemów](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
