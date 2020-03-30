---
title: Rozszerzenie systemu Linux migawka maszyny Wirtualnej dla usługi Azure Backup
description: Wykonywanie spójnej kopii zapasowej maszyny wirtualnej z usługi Azure Backup przy użyciu rozszerzenia migawki maszyny Wirtualnej
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: d0ad54c19749d670f9ab753e1e6d8eb130475ffc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415118"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Rozszerzenie systemu Linux migawka maszyny Wirtualnej dla usługi Azure Backup



Usługa Azure Backup zapewnia obsługę tworzenia kopii zapasowych obciążeń z lokalnych do chmurze i tworzenia kopii zapasowych zasobów w chmurze do magazynu usług odzyskiwania. Usługa Azure Backup używa rozszerzenia migawki maszyny Wirtualnej, aby wykonać spójną kopię zapasową maszyny wirtualnej platformy Azure bez konieczności zamykania maszyny wirtualnej. Rozszerzenie systemu Windows Snapshot na maszynie VM jest publikowane i obsługiwane przez firmę Microsoft w ramach usługi Azure Backup. Usługa Azure Backup zainstaluje rozszerzenie jako część pierwszego zaplanowanego uruchomienia kopii zapasowej, umożliwiającego tworzenie kopii zapasowej. Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform, konfiguracji i opcji wdrażania rozszerzenia migawki maszyny Wirtualnej.

Rozszerzenie VMSnapshot jest wyświetlane w witrynie Azure portal tylko dla nie zarządzanych maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny
Lista obsługiwanych systemów operacyjnych znajduje się w [części Systemy operacyjne obsługiwane przez usługę Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia migawki maszyny Wirtualnej. Rozszerzenie wymaga identyfikatora zadania — identyfikuje zadanie tworzenia kopii zapasowej, które wyzwoliło migawkę na maszynie Wirtualnej, stan obiektu blob uri - gdzie stan operacji migawki jest zapisywany, zaplanowany czas rozpoczęcia migawki, dzienniki uri obiektów blob - gdzie dzienniki odpowiadające zadaniu migawki są zapisywane, objstr- reprezentacja dysków maszyn wirtualnych i metadanych.  Ponieważ te ustawienia powinny być traktowane jako poufne dane, powinny być przechowywane w konfiguracji ustawień chronionych. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że te ustawienia są zalecane do przekazania z usługi Azure Backup tylko w ramach zadania kopii zapasowej.

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
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| identyfikator zadania | e07354cf-041e-4370-929f-25a319ce8933_1 | ciąg |
| commandStartTimeUTCTicks | 6.36458E+17 | ciąg |
| locale | pl-pl | ciąg |
| objectStr | Kodowanie tablicy sas uri- "blobSASUri":\/\/\/["https: sopattna5365.blob.core.windows.net\/vhds vmubuntu1404ltsc201652903941.vhd?sv=2014-02-2014-02-201414&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z\/&\/sp=rw", "https:\/\/&sopattna8461.blob.core.windows.net vhds vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig= 5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubunbuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", " https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%13A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%14%13A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | ciąg |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg |



## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Jednak zalecanym sposobem dodawania rozszerzenia migawki maszyny wirtualnej do maszyny wirtualnej jest włączenie kopii zapasowej na maszynie wirtualnej. Można to osiągnąć za pomocą szablonu Menedżera zasobów.  Przykładowy szablon Menedżera zasobów, który umożliwia tworzenie kopii zapasowych na maszynie wirtualnej, można znaleźć w [Galerii szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można włączyć tworzenie kopii zapasowych na maszynie wirtualnej. Post włącz kopię zapasową, pierwsze zaplanowane zadanie tworzenia kopii zapasowej zainstaluje rozszerzenie migawki maszyny Wirtualnej na maszynie Wirtualnej.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenia

Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku rozwiązywania problemów z tworzeniem kopii zapasowych maszyn wirtualnych platformy Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
