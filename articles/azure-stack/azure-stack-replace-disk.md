---
title: Zastąp dysk fizyczny w stosie Azure | Dokumentacja firmy Microsoft
description: Zawiera opis procesu jak wymienić dysk fizyczny w stosie Azure.
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
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: f168c005c729ae75a5369b80b3dc5eab03ee0243
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30311325"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Zastąp dysk fizyczny w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

W tym artykule opisano ogólny proces, aby zastąpić dysk fizyczny w stosie usługi Azure. W przypadku awarii dysku fizycznego, należy zastąpić go jak najszybciej.

Można użyć tej procedury, zintegrowanych systemów i development kit wdrożenia, których wyłączania dysków.

Zastąpienie rzeczywistego dysku, który kroki będą się różnić w oparciu z dostawcą sprzętu producenta sprzętu (OEM). W dokumentacji udostępnianej przez dostawcę pola jednostkę (FRU) replaceable unit szczegółowy opis kroków, które są specyficzne dla systemu. 

## <a name="review-disk-alert-information"></a>Przejrzyj informacje o dysku alertu
Po awarii dysku, pojawi się alert informujący o tym, że połączenie zostało utracone do dysku fizycznego. 

 ![Utracono łączność wyświetlanie alertów do dysku fizycznego](media/azure-stack-replace-disk/DiskAlert.png)

Po otwarciu alert opis alertu zawiera węzeł jednostki skali i lokalizacji dokładne fizycznego miejsca na dysku, który należy zastąpić. Dalsze stosu Azure pomaga zidentyfikować uszkodzony dysk przy użyciu możliwości wskaźnika LED.

 ## <a name="replace-the-disk"></a>Wymiana dysku

Postępuj zgodnie z dostawcą sprzętu OEM FRU dotyczącymi rzeczywistego dysku zastępczego.

> [!note]
> Zastąpienie dysków do jednego węzła jednostki skali naraz. Poczekaj na zakończenie przed przejściem do następnego węzła jednostki skali zadań naprawy dysku wirtualnego

Aby uniemożliwić korzystanie z dyskiem nieobsługiwany w zintegrowany system, system blokuje dysków, które nie są obsługiwane przez dostawcę. Jeśli spróbujesz użyć dysku nieobsługiwanego nowy alert informuje, że dysk ma zostały poddane kwarantannie z powodu nieobsługiwanego modelu lub oprogramowania układowego.

Po wymienić dysk, stosu Azure automatycznie wykrywa nowy dysk i rozpoczyna proces naprawy dysku wirtualnego.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Sprawdź stan naprawy dysku wirtualnego
 
 Po wymiana dysku, można monitorować stan kondycji dysku wirtualnego i naprawić postęp zadania przy użyciu uprzywilejowanych punktu końcowego. Wykonaj następujące kroki na dowolnym komputerze, który ma łączność sieciową z punktem końcowym uprzywilejowanych.

1. Otwórz sesję programu Windows PowerShell i połącz się uprzywilejowanych punktu końcowego.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Uruchom następujące polecenie, aby wyświetlić kondycję dysku wirtualnego:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Dane wyjściowe polecenia Get-VirtualDisk środowiska PowerShell](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Uruchom następujące polecenie, aby wyświetlić bieżący stan zadania magazynu:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Dane wyjściowe polecenia Get-StorageJob środowiska PowerShell](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Rozwiązywanie problemów z naprawy dysku wirtualnego

Jeśli naprawy dysku wirtualnego zadania jest wyświetlana zablokowane, uruchom następujące polecenie, aby ponownie uruchomić zadania:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
