---
title: Dyskretna instalacja usługi Azure Backup Server w wersji 2
description: Użyj skryptu programu PowerShell, aby dyskretnie zainstalować usługę Azure Backup Server v2. Ten rodzaj instalacji jest również nazywany instalacją nienadzortną.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172240"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Uruchamianie instalacji nienadzorowanego serwera kopii zapasowych platformy Azure

Dowiedz się, jak uruchomić instalację nienadzorowane usługi Azure Backup Server.

Te kroki nie mają zastosowania, jeśli instalujesz usługę Azure Backup Server V1.

## <a name="install-backup-server"></a>Instalowanie serwera kopii zapasowych

1. Na serwerze, na którym jest hostowany serwer kopii zapasowej usługi Azure w wersji 2 lub nowszej, utwórz plik tekstowy. (Plik można utworzyć w Notatniku lub w innym edytorze tekstu). Zapisz plik jako MABSSetup.ini.

2. Wklej następujący kod w pliku MABSSetup.ini. Zastąp tekst wewnątrz\< \>nawiasów ( ) wartościami ze środowiska. Przykładem jest następujący tekst:

   ```text
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

3. Zapisz plik. Następnie w wierszu polecenia z podwyższonym poziomem uprawnień na serwerze instalacyjnym wprowadź następujące polecenie:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Do instalacji można użyć następujących flag:</br>
**/f**: ścieżka pliku ini</br>
**/l**: Ścieżka dziennika</br>
**/i**: Ścieżka instalacji</br>
**/x**: Ścieżka odinstalowywania</br>

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu serwera kopii zapasowej dowiedz się, jak przygotować serwer lub rozpocząć ochronę obciążenia.

- [Przygotowywanie obciążeń serwera kopii zapasowych](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware za pomocą serwera kopii zapasowych](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowej programu SQL Server za pomocą serwera kopii zapasowych](backup-azure-sql-mabs.md)
- [Dodawanie nowoczesnego magazynu kopii zapasowych do serwera kopii zapasowych](backup-mabs-add-storage.md)
