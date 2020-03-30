---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75772952"
---
Ten błąd może wystąpić, gdy usługa Azure File Sync jest niedostępna z poziomu serwera. Ten błąd można rozwiązać, wykonując następujące czynności:

1. Sprawdź, czy `FileSyncSvc.exe` usługa Systemu Windows nie jest zablokowana przez zaporę.
2. Sprawdź, czy port 443 jest otwarty dla połączeń wychodzących z usługą Azure File Sync. Można to zrobić `Test-NetConnection` za pomocą polecenia cmdlet. Adres URL symbolu zastępczego `<azure-file-sync-endpoint>` poniżej można znaleźć w dokumencie [Ustawienia zapory i serwera proxy usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Upewnij się, że konfiguracja serwera proxy jest ustawiona zgodnie z oczekiwaniami. Można to zrobić za pomocą polecenia cmdlet `Get-StorageSyncProxyConfiguration`. Więcej informacji na temat konfigurowania serwera proxy dla usługi Azure File Sync można znaleźć w dokumencie [Ustawienia serwera proxy i zapory usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Polecenie cmdlet Test-StorageSyncNetworkConnectivity służy do sprawdzania łączności sieciowej z punktami końcowymi usługi. Aby dowiedzieć się więcej, zobacz [Testowanie łączności sieciowej z punktami końcowymi usługi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Skontaktuj się z administratorem sieci, aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z łącznością sieciową.
