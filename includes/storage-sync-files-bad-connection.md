---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159888"
---
Ten błąd może wystąpić, gdy usługa Azure File Sync jest niedostępny z serwera. Ten błąd można rozwiązać, klikając pracy przez następujące kroki:

1. Sprawdzić, czy usługa Windows `FileSyncSvc.exe` nie jest blokowany przez zaporę.
2. Sprawdź, czy port 443 został otwarty na połączenia wychodzące do usługi Azure File Sync. Można to zrobić za pomocą `Test-NetConnection` polecenia cmdlet. Adres URL `<azure-file-sync-endpoint>` symbolu zastępczego poniżej można znaleźć w [ustawień serwera proxy i zapory usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) dokumentu. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Upewnij się, że konfiguracja serwera proxy jest zgodne z oczekiwanym. Można to zrobić za pomocą `Get-StorageSyncProxyConfiguration` polecenia cmdlet. Więcej informacji na temat konfigurowania konfiguracji serwera proxy dla usługi Azure File Sync można znaleźć w [ustawień serwera proxy i zapory usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Aby uzyskać dodatkową pomoc, rozwiązywania problemów z łącznością w sieci, skontaktuj się z administratorem sieci.