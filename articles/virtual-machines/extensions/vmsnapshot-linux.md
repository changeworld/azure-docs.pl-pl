---
title: Rozszerzenie migawki maszyny Wirtualnej w systemie Linux dla usługi Azure Backup | Dokumentacja firmy Microsoft
description: Rozmiar kopii zapasowej spójnej aplikacji maszyny wirtualnej z usługi Azure Backup przy użyciu rozszerzenie migawki maszyny Wirtualnej
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhk
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 1d6c89e596fa976161ee28d62885e77b9400a1f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799496"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Rozszerzenie migawki maszyny Wirtualnej w systemie Linux dla usługi Azure Backup



Usługa Azure Backup umożliwia wykonywanie kopii zapasowych obciążeń ze środowiska lokalnego do chmury i wykonywanie kopii zapasowych zasobów w chmurze w magazynie usługi Recovery Services. Usługa Azure Backup używa rozszerzenie migawki maszyny Wirtualnej umożliwiające aplikacji spójnych kopii zapasowych maszyn wirtualnych platformy Azure, bez konieczności zamykania maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej systemu Linux z migawki jest opublikowany i obsługiwane przez firmę Microsoft jako część usługi Azure Backup. Usługa Azure Backup rozszerzenie zostanie zainstalowane jako część pierwszy zaplanowanych kopii zapasowych wyzwalane wpis opcja włączania kopii zapasowych. W tym dokumencie przedstawiono obsługiwanych platform, konfiguracji i opcje wdrażania na potrzeby rozszerzenie migawki maszyny Wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny
Aby uzyskać listę obsługiwanych systemów operacyjnych można znaleźć [systemów operacyjnych obsługiwanych przez usługę Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie migawki maszyny Wirtualnej wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem, gdy podejmujemy kopii zapasowej maszyny wirtualnej.

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat rozszerzenie migawki maszyny Wirtualnej. Rozszerzenie wymaga Identyfikatora zadania — identyfikuje zadanie kopii zapasowej co uruchomiło migawki na maszynie Wirtualnej, identyfikator uri obiektu blob stanu — w którym zapisywana jest stan operacji migawki zaplanowany czas rozpoczęcia migawki, dzienniki obiektów blob identyfikatora uri — gdzie dzienniki odpowiadający migawki zadania są zapisywane, reprezentacja objstr dysków maszyn wirtualnych i metadane.  Ponieważ te ustawienia powinny być traktowane jako poufne dane, powinny być przechowywane w chronionym ustawienia konfiguracji. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że te ustawienia są zalecane do przekazania z usługi Kopia zapasowa Azure tylko jako część zadanie tworzenia kopii zapasowej.

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

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458E + 17 | string |
| ustawienia regionalne | en-us | string |
| objectStr | Kodowanie tablicy identyfikatora uri sygnatury dostępu współdzielonego — "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/wirtualne dyski twarde\/vmubuntu1404ltsc201652903941.vhd? sv = 2014-02-14 & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/\/sopattna8461.blob.core.windows.net\/wirtualne dyski twarde\/vmubuntu1404ltsc-20160629-122418.vhd? sv = 2014-02-14 & sr = b & sig = 2BS % 2FqMwzFMbamT5upwx05v8Q 5S0A6YDWvVwqPAkzWXVy % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/ \/ sopattna8461.blob.Core.Windows.NET\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? sv = 2014-02-14 & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/\/sopattna5365.blob.core.windows.net\/wirtualne dyski twarde\/vmubuntu1404ltsc-20160701-163922.vhd? sv = 2014-02-14 & sr = b & sig = 2BC % 2BNIAork oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/ \/ sopattna5365.blob.Core.Windows.NET\/wirtualne dyski twarde\/vmubuntu1404ltsc-20170705-124311.vhd? sv = 2014-02-14 & sr = b & sig = 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0 ZUM9d28Mvvm % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw "] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Zalecanym sposobem dodawania rozszerzenie migawki maszyny Wirtualnej do maszyny wirtualnej jest jednak opcja włączania kopii zapasowych na maszynie wirtualnej. Można to osiągnąć przy użyciu szablonu usługi Resource Manager.  Przykładowy szablon usługi Resource Manager, który umożliwia tworzenie kopii zapasowych na maszynie wirtualnej można znaleźć na [w galerii platformy Azure Szybki Start](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do włączenia kopii zapasowej na maszynie wirtualnej. Wpis Włącz kopię zapasową pierwszego zaplanowanego zadania tworzenia kopii zapasowej zainstaluje rozszerzenie migawki maszyny wirtualnej na maszynie Wirtualnej.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

Informacje dotyczące rozwiązywania problemów można znaleźć na [kopii zapasowej maszyny Wirtualnej platformy Azure, przewodnik rozwiązywania problemów z](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
