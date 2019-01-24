---
title: Zamienianie dysku fizycznego w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Opisano sposób zastąpienia dysku fizycznego w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.openlocfilehash: d0b455261649fad95a92f7ad75f7af26d633cf5a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476890"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Zamienianie dysku fizycznego w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W tym artykule opisano ogólny proces, aby zastąpić dysk fizyczny w usłudze Azure Stack. Jeśli dysk fizyczny nie powiedzie się, należy zastąpić go jak najszybciej.

Zintegrowane systemy i development kit wdrożenia, których wyłączania dysków, można użyć tej procedury.

Wymiana dysku rzeczywistego, które kroki będą się różnić na podstawie z dostawcą sprzętu producenta sprzętu (OEM). W dokumentacji dostawcy pola jednostkę (FRU) replaceable unit szczegółowy opis kroków, które są specyficzne dla systemu.

## <a name="review-disk-alert-information"></a>Przejrzyj informacje o alertach dysku
Po awarii dysku, otrzymasz alert informujący o tym, czy łączność utracono na dysku fizycznym.

 ![Utracono łączność wyświetlanie alertów fizycznego dysku](media/azure-stack-replace-disk/DiskAlert.png)

Jeśli otworzysz alertu, opis alertu zawiera węzeł jednostki skalowania i lokalizacji dokładnie fizycznego miejsca na dysku, który należy zastąpić. Dodatkowo usługa Azure Stack ułatwia ustalenie uszkodzony dysk przy użyciu diod LED wskaźnika funkcji.

 ## <a name="replace-the-disk"></a>Wymiana dysku

Wykonaj instrukcje FRU dostawcy sprzętu OEM dla rzeczywistego dysku zastępczego.

> [!note]
> Zastąp dysków do jednego węzła jednostki skalowania w danym momencie. Poczekaj na ukończenie przed przejściem do następnego węzła jednostki skalowania zadań naprawy dysku wirtualnego

Aby zapobiec dysku nieobsługiwanego w zintegrowanym systemie, system blokuje wykonywanie dysków, które nie są obsługiwane przez dostawcę. Jeśli spróbujesz użyć dysku nieobsługiwanego nowy alert informujący o tym, czy dysk ma zostały poddane kwarantannie z powodu nieobsługiwanego modelu lub oprogramowania układowego.

Po zamianie dysku usługi Azure Stack umożliwia odnalezienie nowy dysk i automatycznie rozpoczyna proces naprawy dysku wirtualnego.
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Sprawdź stan naprawy dysku wirtualnego
 
 Po zamianie dysku można monitorowania stanu kondycji dysku wirtualnego i naprawić postęp zadania przy użyciu uprzywilejowanych punktu końcowego. Wykonaj poniższe kroki z dowolnego komputera, który ma łączność sieciową do uprzywilejowanych punktu końcowego.

1. Otwórz sesję środowiska Windows PowerShell, a następnie nawiązać połączenie z uprzywilejowanym punktu końcowego.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Uruchom następujące polecenie, aby wyświetlić kondycję dysku wirtualnego:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Dane wyjściowe programu PowerShell Get-VirtualDisk polecenia](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Uruchom następujące polecenie, aby wyświetlić bieżący stan zadania magazynu:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Dane wyjściowe programu PowerShell Get-StorageJob polecenia](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Rozwiązywanie problemów z naprawy dysku wirtualnego

Naprawa dysku wirtualnego zadania pojawia się zablokowane, uruchom następujące polecenie, aby ponownie uruchomić zadania:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
