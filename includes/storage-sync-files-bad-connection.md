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
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391735"
---
Ten błąd może wystąpić, gdy usługa Azure File Sync jest niedostępna z serwera. Ten błąd można rozwiązać, wykonując następujące czynności:

1. Sprawdź, czy usługa systemu Windows `FileSyncSvc.exe` nie jest blokowana przez zaporę.
2. Sprawdź, czy port 443 jest otwarty dla połączeń wychodzących z usługą Azure File Sync. Można to zrobić za pomocą polecenia cmdlet `Test-NetConnection`. Adres URL `<azure-file-sync-endpoint>` z symbolem zastępczym poniżej znajduje się w dokumencie [Azure File Sync serwer proxy i ustawienia zapory](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) . 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Upewnij się, że konfiguracja serwera proxy jest ustawiona zgodnie z oczekiwaniami. Można to zrobić za pomocą polecenia cmdlet `Get-StorageSyncProxyConfiguration`. Więcej informacji na temat konfigurowania konfiguracji serwera proxy dla Azure File Sync można znaleźć w [Azure File Sync serwerze proxy i ustawieniach zapory](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Skontaktuj się z administratorem sieci, aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów z siecią.