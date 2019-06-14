---
title: Instalacja dyskretna z usługi Azure Backup Server w wersji 2
description: Użyj skryptu programu PowerShell do przeprowadzenia instalacji dyskretnej usługi Azure Backup Server w wersji 2. Tego rodzaju instalacji jest również nazywany instalacji nienadzorowanej.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: 66ed5765a91b607bc5b765926c5df87d13ff6a24
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253841"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Uruchomić nienadzorowaną instalację serwera usługi Azure Backup

Dowiedz się, jak uruchomić nienadzorowaną instalację serwera usługi Azure Backup.

Te kroki nie są stosowane, jeśli instalujesz Azure Backup Server w wersji 1.

## <a name="install-backup-server"></a>Zainstaluj serwer kopii zapasowych

1. Na serwerze, który obsługuje usługi Azure Backup Server w wersji 2 lub nowszym, Utwórz plik tekstowy. (Można utworzyć pliku, w Notatniku lub w innym edytorze tekstu.) Zapisz plik jako MABSSetup.ini.

2. Wklej następujący kod w pliku MABSSetup.ini. Zastąp tekst w nawiasie (\< \>) wartościami z używanego środowiska. Poniższy tekst znajduje się przykład:

   ```
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Zapisz plik. Następnie w podwyższonym wierszu polecenia na serwerze instalacji wpisz to polecenie:

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Podczas instalacji, można użyć tych flag:</br>
**/f**: ścieżka do pliku ini</br>
**/l**: Ścieżka dziennika</br>
**/i**: Ścieżka instalacji</br>
**/x**: Odinstaluj ścieżki</br>

## <a name="next-steps"></a>Kolejne kroki
Po zainstalowaniu serwera usługi Backup, Dowiedz się, jak przygotować serwer lub rozpoczęciu ochrony obciążeń.

- [Przygotowywanie obciążeń serwera usługi Backup](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu kopii zapasowej serwera](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowych programu SQL Server przy użyciu kopii zapasowej serwera](backup-azure-sql-mabs.md)
- [Dodaj Modern Backup Storage do tworzenia kopii zapasowej serwera](backup-mabs-add-storage.md)
